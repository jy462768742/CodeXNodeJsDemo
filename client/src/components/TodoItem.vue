<script setup>
const props = defineProps({
  todo: {
    type: Object,
    required: true,
  },
});

const emit = defineEmits(['toggle', 'remove']);

function onToggle() {
  emit('toggle', props.todo);
}

function onRemove() {
  emit('remove', props.todo);
}
</script>

<template>
  <li class="item">
    <label>
      <input type="checkbox" :checked="todo.done" @change="onToggle" />
      <span :class="{ done: todo.done }">{{ todo.title }}</span>
    </label>
    <button class="ghost" @click="onRemove">删除</button>
  </li>
</template>

<style scoped>
.item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  border: 1px solid #e5e7eb;
  padding: 10px 12px;
  border-radius: 12px;
}

.done {
  text-decoration: line-through;
  color: #9ca3af;
}

button.ghost {
  background: transparent;
  color: #6b7280;
  border: none;
  cursor: pointer;
}
</style>
