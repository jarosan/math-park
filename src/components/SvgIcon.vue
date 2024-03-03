<template>
  <div class="inline-block">
    <svg
      viewBox="0 0 24 24"
      :width="`${size}px`"
      :height="`${size}px`"
      :class="{ '-scale-y-100': flipVertically, '-scale-x-100': flipHorizontally }"
      class="fill-current"
      xmlns="http://www.w3.org/2000/svg"
    >
      <path :d="path" />
    </svg>
  </div>
</template>

<script lang="ts">
export default { name: 'SvgIcon' }
</script>

<script lang="ts" setup>
export interface Props {
  path: string
  size?: number
  flipVertically?: boolean
  flipHorizontally?: boolean
}

withDefaults(defineProps<Props>(), {
  rotate: false,
  size: 16,
})
</script>
