<script setup>
import { ref } from 'vue';

const emit = defineEmits(['submit']);
const title = ref('');

function onSubmit() {
  const value = title.value.trim();
  if (!value) return;
  emit('submit', value);
  title.value = '';
}
</script>

<template>
  <form class="row" @submit.prevent="onSubmit">
    <input v-model="title" placeholder="输入一个待办" />
    <button type="submit">添加</button>
  </form>
</template>

<style scoped>
.row {
  display: flex;
  gap: 8px;
  margin: 16px 0;
}

input {
  flex: 1;
  padding: 10px 12px;
  border: 1px solid #d1d5db;
  border-radius: 10px;
}

button {
  border: none;
  background: #111827;
  color: white;
  padding: 10px 14px;
  border-radius: 10px;
  cursor: pointer;
}
</style>
