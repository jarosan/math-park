<template>
  <div class="mt-6 border">
    <MathQuestion v-if="currentQuestion" :question="currentQuestion" @answer="setQuestionAnswer"/>
  </div>

  <button v-if="!currentQuestion" @click.stop="startQuiz">start</button>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'
import MathQuestion, { type Question } from '@/components/MathQuestion.vue'
const questions = ref<Question[]>()
const currentQuestionIndex = ref<number>()

const currentQuestion = computed(() => {
  if (!questions.value || currentQuestionIndex.value === undefined) return

  return questions.value[currentQuestionIndex.value]
})

function startQuiz() {
  questions.value = [
    {left: 2, right: 0, operation: 'multiply'},
    {left: 2, right: 1, operation: 'multiply'},
    {left: 2, right: 2, operation: 'multiply'},
    {left: 2, right: 3, operation: 'multiply'},
    {left: 2, right: 4, operation: 'multiply'},
    {left: 2, right: 5, operation: 'multiply'},
    {left: 2, right: 6, operation: 'multiply'},
    {left: 2, right: 7, operation: 'multiply'},
    {left: 2, right: 8, operation: 'multiply'},
    {left: 2, right: 9, operation: 'multiply'},
    {left: 2, right: 10, operation: 'multiply'},
  ]

  currentQuestionIndex.value = 0
}

function setQuestionAnswer(isCorrect: boolean) {
  console.log('question is correct:', isCorrect)
}
</script>
