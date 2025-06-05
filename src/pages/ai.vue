<template>
  <v-layout class="rounded rounded-md border">
    <naga></naga>
    <v-main class="d-flex align-center justify-center">
      <v-container class="d-flex align-center justify-center flex-column">
        <h1>手写数字识别</h1>
        <canvas ref="canvas" width="280" height="280"
          style="border: 1px solid black; image-rendering: pixelated; touch-action: none;background-color: white;"
          @mousedown="startDrawing" @mousemove="draw" @mouseup="stopDrawing" @touchstart="handleTouchStart"
          @touchmove="handleTouchMove" @touchend="stopDrawing"></canvas>
        <br>
        <div>
          <v-btn color="success" @click="clearCanvas">清空画布</v-btn>
          <v-btn color="success" :disabled="isRecognizing" @click="recognize">{{ isRecognizing ? '识别中...' : '识别数字'
          }}</v-btn>
        </div>
        <div v-if="prediction !== null" class="result">
          识别结果: <strong>{{ prediction }}</strong>
        </div>
        <div v-if="probabilities !== null" class="result">
          置信度：{{ probabilities }}
        </div>
        <div v-if="error" class="error">{{ error }}</div>
      </v-container>
    </v-main>
  </v-layout>
</template>

<script setup lang="ts">
import { ref, onMounted, onUnmounted } from 'vue';
import naga from '@/components/naga.vue';
// 类型定义
interface Point {
  x: number;
  y: number;
}

// DOM 引用和状态
const canvas = ref<HTMLCanvasElement | null>(null);
const isDrawing = ref(false);
const prediction = ref<number | null>(null);
const error = ref<string | null>(null);
const isRecognizing = ref(false);
const probabilities = ref(null);
// 绘图上下文
let ctx: CanvasRenderingContext2D;

// 初始化画布
onMounted(() => {
  if (!canvas.value) return;

  ctx = canvas.value.getContext('2d')!;
  ctx.lineWidth = 25;
  ctx.lineCap = 'round';
  ctx.strokeStyle = 'black';

  // 阻止移动端默认行为
  const preventScroll = (e: Event) => {
    if (isDrawing.value) e.preventDefault();
  };

  canvas.value.addEventListener('touchmove', preventScroll, { passive: false });

  onUnmounted(() => {
    canvas.value?.removeEventListener('touchmove', preventScroll);
  });
});

// 获取画布坐标（兼容触摸/鼠标事件）
const getCanvasCoordinates = (e: MouseEvent | TouchEvent): Point => {
  if (!canvas.value) return { x: 0, y: 0 };

  const rect = canvas.value.getBoundingClientRect();
  let clientX: number, clientY: number;

  if ('touches' in e) {
    clientX = e.touches[0].clientX;
    clientY = e.touches[0].clientY;
  } else {
    clientX = e.clientX;
    clientY = e.clientY;
  }

  return {
    x: clientX - rect.left,
    y: clientY - rect.top
  };
};

// 开始绘制
const startDrawing = (e: MouseEvent | TouchEvent) => {
  isDrawing.value = true;
  const { x, y } = getCanvasCoordinates(e);
  ctx.beginPath();
  ctx.moveTo(x, y);
};

// 绘制过程
const draw = (e: MouseEvent | TouchEvent) => {
  if (!isDrawing.value) return;

  const { x, y } = getCanvasCoordinates(e);
  ctx.lineTo(x, y);
  ctx.stroke();
  ctx.beginPath();
  ctx.moveTo(x, y);
};

// 结束绘制
const stopDrawing = () => {
  isDrawing.value = false;
  ctx.beginPath();
};

// 触摸事件处理
const handleTouchStart = (e: TouchEvent) => {
  e.preventDefault();
  startDrawing(e);
};

const handleTouchMove = (e: TouchEvent) => {
  e.preventDefault();
  draw(e);
};

// 清空画布
const clearCanvas = () => {
  if (!canvas.value) return;

  ctx.clearRect(0, 0, canvas.value.width, canvas.value.height);
  probabilities.value = null;
  prediction.value = null;
  error.value = null;
};

// 识别数字
const recognize = async () => {
  if (!canvas.value) return;

  isRecognizing.value = true;
  error.value = null;
  const backendUrl = 'https://py.202718.xyz';
  // const backendUrl = 'http://localhost:8000';
  try {
    // 获取 Base64 图像
    const imageBase64 = canvas.value.toDataURL('image/png');
    //console.log(imageBase64)
    const response = await fetch(`${backendUrl}/api/predict`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ image: imageBase64 }),
    });

    if (!response.ok) {
      throw new Error(`请求失败: ${response.status}`);
    }

    const result = await response.json();
    prediction.value = result.prediction;
    probabilities.value = result.probabilities;
  } catch (err) {
    console.error('识别失败:', err);
    error.value = '识别失败，请重试或检查网络连接';
  } finally {
    isRecognizing.value = false;
  }
};
</script>
