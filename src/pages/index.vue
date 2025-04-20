<template>
    <v-layout class="rounded rounded-md border">
        <naga></naga>
        <v-main class="d-flex align-center justify-center">
            <v-container>
                <v-sheet border="dashed md" color="surface-light" rounded="lg" width="100%">
                    <v-card :color="color" :variant="variant" class="mx-auto" hover prepend-icon="mdi-image-multiple">
                        <v-card-title>
                            相册
                        </v-card-title>
                        <v-card-text>
                            来看看班级照片吧！
                        </v-card-text>
                        <v-carousel cycle show-arrows="hover">
                            <v-carousel-item src="https://cdn.vuetifyjs.com/images/cards/docks.jpg"
                                cover></v-carousel-item>

                            <v-carousel-item src="https://cdn.vuetifyjs.com/images/cards/sunshine.jpg"
                                cover></v-carousel-item>
                        </v-carousel>
                        <v-card-actions>
                            <v-btn color="info" to="/photos">进入相册</v-btn>
                        </v-card-actions>
                    </v-card>
                </v-sheet>
                <v-sheet border="dashed md" color="surface-light" rounded="lg" width="100%">
                    <v-card :color="color" :variant="variant" class="mx-auto" hover prepend-icon="mdi-image-multiple">
                        <v-card-title>
                            深度学习项目
                        </v-card-title>
                        <v-card-text>
                            基于深度学习的手写数字识别项目
                        </v-card-text>
                        <v-card-actions>
                            <v-btn color="info" to="/ai">进入深度学习项目</v-btn>
                        </v-card-actions>
                    </v-card>
                </v-sheet>
            </v-container>
        </v-main>
    </v-layout>
</template>
<script setup>
import naga from '@/components/naga.vue';
</script>