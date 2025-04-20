<template>
    <v-layout class="rounded rounded-md border">
        <naga></naga>
        <v-main class="d-flex align-center justify-center">
            <v-container>
                <v-sheet border="dashed md" color="surface-light" rounded="lg" width="100%">
                    <v-card :color="color" :variant="variant" class="mx-auto" hover>
                        <v-carousel cycle show-arrows="hover">
                            <v-carousel-item src="https://cdn.vuetifyjs.com/images/cards/docks.jpg"
                                cover></v-carousel-item>

                            <v-carousel-item src="https://cdn.vuetifyjs.com/images/cards/sunshine.jpg"
                                cover></v-carousel-item>
                        </v-carousel>
                    </v-card>
                </v-sheet>
            </v-container>
        </v-main>
    </v-layout>
</template>
<script setup>
import naga from '@/components/naga.vue';
</script>