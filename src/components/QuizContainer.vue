<template>
  <div  class="mt-10">
    <div v-if="quizFinished">
      <h2>Quiz beendet</h2>
      <p class="text-xl mt-4">{{ correctCount }} / {{ questions!.length }} richtig</p>
      <button class="button mt-6" @click.stop="resetQuiz">neu starten</button>
    </div>

    <div v-else-if="!currentQuestion" class="mt-12 flex flex-col items-center">
      <h2 class="text-2xl">Bereit?</h2>

      <div class="flex gap-4 mt-6">
        <button class="button" :class="{ 'bg-emerald-600': mode === 'multiply' }" @click="mode = 'multiply'">Malnehmen</button>
        <button class="button" :class="{ 'bg-emerald-600': mode === 'divide' }" @click="mode = 'divide'">Teilen</button>
      </div>

      <div v-for="i in Array.from(new Array(8), (x, i) => i + 2)" :key="i" class="flex gap-6 mt-4">
        <button class="button" @click.stop="startQuiz(i, false)">{{ i }}{{ mode === 'multiply' ? 'x' : ':' }}</button>
        <button class="button" @click.stop="startQuiz(i, true)">
          {{ i }}{{ mode === 'multiply' ? 'x' : ':' }}
          <SvgIcon :path="mdiDice5Outline" :size="24" />
        </button>
      </div>
    </div>

    <div v-else>
      <p class="text-sm text-neutral-500 mb-4">Aufgabe {{ currentQuestionIndex! + 1 }} von {{ questions!.length }}</p>
      <MathQuestion v-if="currentQuestion" :key="currentQuestionIndex" :question="currentQuestion.question" @answer="setQuestionAnswer"/>

      <button v-if="currentQuestionAnswered" class="button bg-emerald-600" @click="proceedToNextQuestion">weiter</button>
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
const mode = ref<'multiply' | 'divide'>('multiply')
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

const correctCount = computed(() => questions.value?.filter(q => q.answeredCorrectly).length ?? 0)

function resetQuiz() {
  questions.value = undefined
  currentQuestionIndex.value = undefined
}

function startQuiz(i: number, random: boolean) {
  let possibleQuestions: AnswereableQuestion[]

  if (mode.value === 'divide') {
    possibleQuestions = Array.from({ length: 11 }, (_, j) => ({
      question: { left: i * j, right: i, operation: 'divide' as const },
    }))
  } else {
    possibleQuestions = Array.from({ length: 11 }, (_, j) => ({
      question: { left: i, right: j, operation: 'multiply' as const },
    }))
  }

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
  if (!questions.value || currentQuestionIndex.value === undefined) throw new Error('something went wrong')

  questions.value[currentQuestionIndex.value].answeredCorrectly = isCorrect
}

function proceedToNextQuestion() {
  if (currentQuestionIndex.value === undefined) return

  currentQuestionIndex.value += 1
}
</script>
