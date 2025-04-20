<template>
    <v-layout class="rounded rounded-md border">
        <naga></naga>
        <v-main class="d-flex align-center justify-center ga-4 flex-column">
            <div class="d-flex align-center justify-center ga-4">
                <div class="d-flex align-center justify-center flex-column">
                    <p>选择起始座位的那天</p>
                    <v-date-picker color="primary" v-model:model-value="rawTime"></v-date-picker>
                </div>
                <div class="d-flex align-center justify-center flex-column">
                    <p>选择一天</p>
                    <v-date-picker color="primary" v-model:model-value="selectedTime"></v-date-picker>
                </div>
            </div>
            <v-btn color="success" @click="sub">查询</v-btn>
            <div class="d-flex align-center justify-center ga-4">
                <p>门</p>
                <v-table>
                    <caption style="caption-side: bottom;">讲台</caption>
                    <thead>
                        <tr>
                            <th scope="col">第一组</th>
                            <th scope="col">第二组</th>
                            <th scope="col">第三组</th>
                            <th scope="col">第四组</th>
                            <th scope="col">第五组</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(row, rowIndex) in formattedData" :key="rowIndex">
                            <td v-for="(seat, colIndex) in row" :key="colIndex">
                                <template v-if="seat.first === 'x'">
                                    <span style="color: red">——</span>
                                </template>
                                <template v-else>
                                    <p v-if="seat.second !== 'None'">{{ seat.first }}/ {{ seat.second }}</p>
                                    <p v-else>{{ seat.first }}</p>
                                </template>
                            </td>
                        </tr>
                    </tbody>
                </v-table>
                <p>窗</p>
            </div>
        </v-main>
    </v-layout>
</template>

<script setup lang="ts">
import naga from '@/components/naga.vue';
import { ref } from 'vue';
let rawTime = ref(new Date());
let selectedTime = ref(new Date());
function getWeeksBetween(startDate, endDate) {
    const getMonday = (date) => {
        const d = new Date(date);
        d.setHours(0, 0, 0, 0);
        const day = d.getDay(); // 0（周日）到6（周六）
        const diff = day === 0 ? 6 : day - 1; // 调整到最近的周一
        d.setDate(d.getDate() - diff);
        return d;
    };

    const start = new Date(startDate);
    const end = new Date(endDate);

    const mondayStart = getMonday(start);
    const mondayEnd = getMonday(end);

    // 计算两个周一之间的天数差
    const timeDiff = mondayEnd.getTime() - mondayStart.getTime();
    const dayDiff = timeDiff / (1000 * 3600 * 24);

    // 周数差（取绝对值）
    const weeks = Math.abs(dayDiff / 7);

    return weeks;
}
let formattedData = ref(null);
function sub() {

    const weeksBetween = getWeeksBetween(rawTime.value, selectedTime.value);
    const response = fetch('https://api.202718.xyz/api/seat', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ time: weeksBetween }),
    })
    response.then(res => res.json()).then(data => {
        console.log(data);
        // 将列式数据结构转换为行式
        const rows = [];
        const colCount = Object.keys(data).length;
        const rowCount = data[0].length;

        for (let rowIndex = 0; rowIndex < rowCount; rowIndex++) {
            const row = [];
            for (let colIndex = 0; colIndex < colCount; colIndex++) {
                const column = data[colIndex.toString()];
                if (column && column[rowIndex]) {
                    row.push(column[rowIndex]);
                } else {
                    row.push({ first: 'x', second: 'None' }); // 处理缺失数据
                }
            }
            rows.push(row);
        }
        console.log(rows);
        formattedData.value = rows;
    }).catch(error => {
        console.error('Error:', error);
    });
}
</script>