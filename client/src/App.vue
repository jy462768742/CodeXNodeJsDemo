<script setup>
import { onMounted, ref } from 'vue';
import TodoForm from './components/TodoForm.vue';
import TodoList from './components/TodoList.vue';

const API_BASE = import.meta.env.VITE_API_BASE || 'http://localhost:3001';

const todos = ref([]);
const loading = ref(false);
const error = ref('');

async function fetchTodos() {
  loading.value = true;
  error.value = '';
  try {
    const res = await fetch(`${API_BASE}/api/todos`);
    if (!res.ok) throw new Error('Failed to load');
    todos.value = await res.json();
  } catch (e) {
    error.value = '加载失败，请确认后端已启动。';
  } finally {
    loading.value = false;
  }
}

async function addTodo(title) {
  const value = title.trim();
  if (!value) return;
  error.value = '';
  try {
    const res = await fetch(`${API_BASE}/api/todos`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ title: value }),
    });
    if (!res.ok) throw new Error('Failed to create');
    const created = await res.json();
    todos.value = [created, ...todos.value];
  } catch (e) {
    error.value = '创建失败，请检查后端日志。';
  }
}

async function toggleTodo(todo) {
  error.value = '';
  try {
    const res = await fetch(`${API_BASE}/api/todos/${todo.id}`, {
      method: 'PATCH',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ done: !todo.done }),
    });
    if (!res.ok) throw new Error('Failed to update');
    const updated = await res.json();
    todos.value = todos.value.map(t => (t.id === updated.id ? updated : t));
  } catch (e) {
    error.value = '更新失败。';
  }
}

async function removeTodo(todo) {
  error.value = '';
  try {
    const res = await fetch(`${API_BASE}/api/todos/${todo.id}`, {
      method: 'DELETE',
    });
    if (!res.ok) throw new Error('Failed to delete');
    todos.value = todos.value.filter(t => t.id !== todo.id);
  } catch (e) {
    error.value = '删除失败。';
  }
}

onMounted(fetchTodos);
</script>

<template>
  <div class="page">
    <div class="card">
      <h1>Todo Demo</h1>
      <p class="subtitle">最小全栈可执行：Vue + Express + PostgreSQL</p>

      <TodoForm @submit="addTodo" />

      <p v-if="error" class="error">{{ error }}</p>
      <p v-if="loading" class="muted">加载中...</p>

      <TodoList v-else :todos="todos" @toggle="toggleTodo" @remove="removeTodo" />
    </div>
  </div>
</template>

<style scoped>
.page {
  min-height: 100vh;
  display: grid;
  place-items: center;
  background: #f6f7fb;
  color: #1f2937;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

.card {
  width: min(640px, 92vw);
  background: white;
  border: 1px solid #e5e7eb;
  border-radius: 16px;
  padding: 24px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.08);
}

.subtitle {
  color: #6b7280;
  margin-top: -4px;
}

.error {
  color: #b91c1c;
}

.muted {
  color: #6b7280;
}
</style>
