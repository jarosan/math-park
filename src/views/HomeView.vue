<template>
  <main class="px-4 container mx-auto max-w-xl">
    <h1 class="mt-6 text-5xl text-center flex items-center justify-center">
      <SvgIcon :path="mdiCalculatorVariantOutline" :size="42" />
      Math Park
    </h1>
    <QuizContainer />
  </main>
</template>

<script setup lang="ts">
import QuizContainer from '@/components/QuizContainer.vue'
import { mdiCalculatorVariantOutline } from '@mdi/js';
import SvgIcon from '@/components/SvgIcon.vue';
</script>
