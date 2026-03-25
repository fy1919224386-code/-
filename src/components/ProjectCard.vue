<template>
  <div class="mb-3 border-bottom pb-3">
    <div class="d-flex justify-content-between align-items-center">
      <h6 class="mb-1">{{ project.name }}</h6>
      <small class="text-muted">{{ project.date }}</small>
    </div>
    <p class="mb-1">{{ project.summary }}</p>
    <button class="btn btn-sm btn-link p-0" @click="showDetail">查看详情</button>
  </div>
</template>

<script setup>
const props = defineProps({
  project: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['show-detail'])

const showDetail = () => {
  emit('show-detail', props.project)
}
</script>

<style scoped>
/* 样式统一在主组件管理 */
</style>