<template>
  <button type="button" class="btn btn-outline-primary btn-sm me-2 mb-2" @click="handleClick">
    {{ skill.name }}
  </button>
</template>

<script setup>
const props = defineProps({
  skill: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['click'])

const handleClick = () => {
  emit('click', props.skill)
}
</script>

<style scoped>
/* 样式统一在主组件管理 */
</style>