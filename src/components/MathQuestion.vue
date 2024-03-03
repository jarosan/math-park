<template>
  <div class="flex gap-6">
    <div class="grow">
      <div class="flex gap-2 items-center">
        <div class="text-2xl">
          {{ question.left }}
          {{ operationSymbol }}
          {{ question.right }}
          =
        </div>

        <input v-model="answer" max="100" class="border-2 border-neutral-600 w-[70px] rounded bg-white" type="number" name="answer">
        <SvgIcon :path="mdiBackspaceOutline" :size="24" class="cursor-pointer" @click="answer = ''"/>
      </div>

      <div v-if="alreadyAnswered">
        <div v-if="isAnswerCorrect">
          You are correct! 🎉
        </div>
        <div v-else>
          You are wrong 😢
          <br />
          Correct answer is: <strong>{{ correctAnswer }}</strong>
        </div>
      </div>
    </div>

    <div class="shrink-0 flex flex-col gap-1">
      <div class="flex gap-1">
        <button class="keypad_button" @click="keyPadPressed(1)">1</button>
        <button class="keypad_button" @click="keyPadPressed(2)">2</button>
        <button class="keypad_button" @click="keyPadPressed(3)">3</button>
      </div>
      <div class="flex gap-1">
        <button class="keypad_button" @click="keyPadPressed(4)">4</button>
        <button class="keypad_button" @click="keyPadPressed(5)">5</button>
        <button class="keypad_button" @click="keyPadPressed(6)">6</button>
      </div>
      <div class="flex gap-1">
        <button class="keypad_button" @click="keyPadPressed(7)">7</button>
        <button class="keypad_button" @click="keyPadPressed(8)">8</button>
        <button class="keypad_button" @click="keyPadPressed(9)">9</button>
      </div>
      <div class="text-center">
        <button class="keypad_button" @click="keyPadPressed(0)">0</button>
      </div>
    </div>
  </div>

  <button v-if="!alreadyAnswered" class="button" :disabled="answer === ''" @click.stop="answerQuestion">answer</button>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';
import { mdiBackspaceOutline } from '@mdi/js';
import SvgIcon from './SvgIcon.vue';

export type Question = {
  left: number,
  right: number,
  operation: 'add' | 'substract' | 'multiply' | 'divide'
}

const props = defineProps<{
  question: Question
}>()

const emit = defineEmits<{
  answer: [isCorrect: boolean]
}>()

const answer = ref<number | ''>('')

const alreadyAnswered = ref(false)

const operationSymbol = computed(() => {
  switch(props.question.operation) {
    case 'add':
      return '+'
    case 'substract':
      return '-'
    case 'multiply':
      return '*'
    case 'divide':
      return ':'
    default:
      throw new Error('Unreachable code')
  }
})

const correctAnswer = computed(() => {
  const { left, right} = props.question

  switch(props.question.operation) {
    case 'add':
      return left + right
    case 'substract':
      return left - right
    case 'multiply':
      return left * right
    case 'divide':
      return left / right
    default:
      throw new Error('Unreachable code')
  }
})

const isAnswerCorrect = computed(() => {
  return answer.value === correctAnswer.value
})

function answerQuestion() {
  if (answer.value === '') return

  alreadyAnswered.value = true
  emit('answer', isAnswerCorrect.value)
}

function keyPadPressed(number: number) {
  if (answer.value === '') {
    answer.value = number
  } else {
    answer.value = answer.value * 10 + number
  }
}
</script>

<style scoped>
.keypad_button {
  @apply button bg-stone-400 text-black w-10;
}

/* Chrome, Safari, Edge, Opera */
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

/* Firefox */
input[type=number] {
  -moz-appearance: textfield;
}
</style>
