<template>
  {{ question.left }}
  {{ operationSymbol }}
  {{ question.right }}
  :
  <div class="border inline-block rounded">
    <input v-model="answer" type="number" name="fuu">
  </div>

  <div>
    <button :disabled="answer === ''" @click.stop="answerQuestion">answer</button>
  </div>
</template>

<script setup lang="ts">
import { computed, ref } from 'vue';

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

function isAnswerCorrect(answer: number) {
  return answer === correctAnswer.value
}

function answerQuestion() {
  if (answer.value === '') return

  emit('answer', isAnswerCorrect(answer.value))
}
</script>
