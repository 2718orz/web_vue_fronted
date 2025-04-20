<template>
    <v-navigation-drawer v-model="bar" :rail="rail" permanent @click="rail = false; bnticon = 'mdi-chevron-left'"
        image="https://tse1-mm.cn.bing.net/th/id/OIP-C.zpQiikU9y4h8qcUofwOuyQHaFj?rs=1&pid=ImgDetMain" theme="dark">
        <v-list-item title="十八班网站">
            <template v-slot:append>
                <v-btn :icon="bnticon" variant="text"
                    @click.stop="rail = !rail; bnticon = bnticon === 'mdi-chevron-left' ? 'mdi-chevron-right' : 'mdi-chevron-left'"></v-btn>
            </template>
        </v-list-item>
        <v-divider></v-divider>
        <v-list density="compact" nav>
            <v-list-item prepend-icon="mdi-home-city" title="主页" to="/"></v-list-item>
            <v-list-item prepend-icon="mdi-image-multiple" title="相册" to="/photos"></v-list-item>
            <v-list-item prepend-icon="mdi-television" title="深度学习项目" to="/ai"></v-list-item>
            <v-list-item prepend-icon="mdi-television" title="座位查询" to="/seat"></v-list-item>
        </v-list>
        <v-list-item bottom>
            <v-switch label="深色主题" v-model="theme.global.name.value" inset true-value="dark" false-value="light"
                v-show="!rail"></v-switch>
        </v-list-item>
    </v-navigation-drawer>
</template>
<script setup>
import { ref } from 'vue';
import { useTheme } from 'vuetify'

const theme = useTheme()
let bar = ref(true)
let rail = ref(false)
let bnticon = ref('mdi-chevron-left')
</script>