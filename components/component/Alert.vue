<template>
  <transition name="slide-fade">
    <div
      v-if="visible"
      :class="[
        'fixed top-4 right-4 px-4 py-3 rounded-lg shadow-lg text-white font-medium z-50',
        type === 'success' ? 'bg-green-500' : 'bg-red-500'
      ]"
    >
      {{ message }}
    </div>
  </transition>
</template>

<script setup lang="ts">
import { ref, watch } from "vue"

const props = defineProps<{
  message: string
  type: "success" | "error"
  show: boolean
}>()

const visible = ref(false)

watch(
  () => props.show,
  (newVal) => {
    visible.value = newVal
    if (newVal) {
      setTimeout(() => {
        visible.value = false
      }, 5000) // 5 sec baad auto hide
    }
  }
)
</script>

<style scoped>
.slide-fade-enter-active,
.slide-fade-leave-active {
  transition: all 0.5s ease;
}
.slide-fade-enter-from,
.slide-fade-leave-to {
  opacity: 0;
  transform: translateX(100px);
}
</style>
