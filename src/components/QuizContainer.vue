<template>
  <div  class="mt-10">
    <div v-if="quizFinished">
      <h2>Quiz finished</h2>
      <button class="button mt-6" @click.stop="questions = undefined">restart</button>
    </div>

    <div v-else-if="!currentQuestion" class="mt-12 flex flex-col items-center">
      <h2 class="text-2xl">Ready to start?</h2>

      <div v-for="i in Array.from(new Array(8), (x, i) => i + 2)" :key="i" class="flex gap-6 mt-4">
        <button class="button" @click.stop="startQuiz(i, false)">{{ i }}x</button>
        <button class="button" @click.stop="startQuiz(i, true)">
          {{ i }}x
          <SvgIcon :path="mdiDice5Outline" :size="24" />
        </button>
      </div>
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
import { mdiDice5Outline } from '@mdi/js'
import SvgIcon from '@/components/SvgIcon.vue'
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

function startQuiz(i: number, random: boolean) {
  let possibleQuestions = [
    { question: { left: i, right: 0, operation: 'multiply' } },
    { question: { left: i, right: 1, operation: 'multiply' } },
    { question: { left: i, right: 2, operation: 'multiply' } },
    { question: { left: i, right: 3, operation: 'multiply' } },
    { question: { left: i, right: 4, operation: 'multiply' } },
    { question: { left: i, right: 5, operation: 'multiply' } },
    { question: { left: i, right: 6, operation: 'multiply' } },
    { question: { left: i, right: 7, operation: 'multiply' } },
    { question: { left: i, right: 8, operation: 'multiply' } },
    { question: { left: i, right: 9, operation: 'multiply' } },
    { question: { left: i, right: 10, operation: 'multiply' } },
  ] as AnswereableQuestion[]

  if (random) shuffleArray(possibleQuestions)

  questions.value = possibleQuestions
  currentQuestionIndex.value = 0
}

function shuffleArray(array: AnswereableQuestion[]) {
  for (let i = array.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [array[i], array[j]] = [array[j], array[i]];
  }
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
