<template>
  <div  class="mt-10">
    <div v-if="quizFinished">
      <h2>Quiz finished</h2>
      <button class="button mt-6" @click.stop="startQuiz">restart</button>
    </div>

    <div v-else-if="!currentQuestion">
      <h2 class="text-2xl">Ready to start?</h2>
      <button class="button mt-6" @click.stop="startQuiz">GO</button>
    </div>

    <div v-else>
      <MathQuestion v-if="currentQuestion" :key="currentQuestionIndex" :question="currentQuestion.question" @answer="setQuestionAnswer"/>

      <button v-if="currentQuestionAnswered" class="button bg-emerald-600" @click="proceedToNextQuestion">next</button>
    </div>


    <div class="mt-40 flex gap-1">
      <div
        v-for="(question, i) in questions"
        :key="i"
        class="grow rounded text-center"
        :class="{
          'bg-gray-300': question.answeredCorrectly === undefined,
          'bg-green-300': question.answeredCorrectly,
          'bg-red-300': !question.answeredCorrectly && question.answeredCorrectly !== undefined,
        }"
      >
        &nbsp;
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue'
import MathQuestion, { type Question } from '@/components/MathQuestion.vue'

type AnswereableQuestion = {
  question: Question
  answeredCorrectly?: boolean
}
const questions = ref<AnswereableQuestion[]>()
const currentQuestionIndex = ref<number>()

const currentQuestion = computed(() => {
  if (!questions.value || currentQuestionIndex.value === undefined) return

  return questions.value[currentQuestionIndex.value]
})

const currentQuestionAnswered = computed(() => {
  return currentQuestion.value?.answeredCorrectly !== undefined
})

const quizFinished = computed(() => currentQuestionIndex.value && questions.value && questions.value.length <= currentQuestionIndex.value)

function startQuiz() {
  questions.value = [
    { question: { left: 2, right: 0, operation: 'multiply' } },
    { question: { left: 2, right: 1, operation: 'multiply' } },
    { question: { left: 2, right: 2, operation: 'multiply' } },
    { question: { left: 2, right: 3, operation: 'multiply' } },
    { question: { left: 2, right: 4, operation: 'multiply' } },
    { question: { left: 2, right: 5, operation: 'multiply' } },
    { question: { left: 2, right: 6, operation: 'multiply' } },
    { question: { left: 2, right: 7, operation: 'multiply' } },
    { question: { left: 2, right: 8, operation: 'multiply' } },
    { question: { left: 2, right: 9, operation: 'multiply' } },
    { question: { left: 2, right: 10, operation: 'multiply' } },
  ]

  currentQuestionIndex.value = 0
}

function setQuestionAnswer(isCorrect: boolean) {
  console.log('question is correct:', isCorrect)

  if (!questions.value || currentQuestionIndex.value === undefined) throw new Error('something went wrong')

  questions.value[currentQuestionIndex.value].answeredCorrectly = isCorrect
}

function proceedToNextQuestion() {
  if (currentQuestionIndex.value === undefined) return

  currentQuestionIndex.value += 1
}
</script>
