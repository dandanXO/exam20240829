<template>
  <div class="todo-list-app">
    <h1>待辦事項清單</h1>
    <div class="input-group">
      <input v-model="newTodo" placeholder="輸入新事項" @keyup.enter="addTodo" />
      <button @click="addTodo">新增</button>
    </div>

    <div class="filters">
      <button :class="{ active: filter === 'all' }" @click="setFilter('all')">全部</button>
      <button :class="{ active: filter === 'completed' }" @click="setFilter('completed')">
        已完成
      </button>
      <button :class="{ active: filter === 'pending' }" @click="setFilter('pending')">
        未完成
      </button>
    </div>

    <ul>
      <li v-for="(todo, index) in filteredTodos" :key="index">
        <input type="checkbox" v-model="todo.completed" @change="saveTodos" />
        <span :style="{ textDecoration: todo.completed ? 'line-through' : 'none' }">
          {{ todo.text }}
        </span>
        <button @click="removeTodo(index)">刪除</button>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue'

const todos = ref([])

const newTodo = ref('')

const filter = ref('all')

const addTodo = () => {
  if (newTodo.value.trim() !== '') {
    todos.value.push({ text: newTodo.value, completed: false })
    newTodo.value = ''
    saveTodos()
  }
}

const removeTodo = (index) => {
  todos.value.splice(index, 1)
  saveTodos()
}

const setFilter = (newFilter) => {
  filter.value = newFilter
}

const filteredTodos = computed(() => {
  if (filter.value === 'completed') {
    return todos.value.filter((todo) => todo.completed)
  } else if (filter.value === 'pending') {
    return todos.value.filter((todo) => !todo.completed)
  } else {
    return todos.value
  }
})

const saveTodos = () => {
  localStorage.setItem('todos', JSON.stringify(todos.value))
}

const loadTodos = () => {
  const savedTodos = localStorage.getItem('todos')
  if (savedTodos) {
    todos.value = JSON.parse(savedTodos)
  }
}

onMounted(loadTodos)

watch(todos, saveTodos, { deep: true })
</script>

<style scoped>
.todo-list-app {
  max-width: 400px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 10px;

  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

h1 {
  text-align: center;
  color: #333;
}

.input-group {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
}

input[type='text'] {
  flex: 1;
  padding: 10px;
  border-radius: 5px;
  border: 1px solid #ccc;
  margin-right: 10px;
}

button {
  padding: 10px;
  border: none;
  border-radius: 5px;
  background-color: #007bff;
  color: white;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}

.filters {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.filters button {
  margin: 0 5px;
  background-color: #f0f0f0;
}

.filters button.active {
  background-color: #007bff;
  color: white;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  border-bottom: 1px solid #ddd;
}

li:last-child {
  border-bottom: none;
}

li span {
  flex: 1;
  margin-left: 10px;
}

li button {
  background-color: #dc3545;
}

li button:hover {
  background-color: #c82333;
}
</style>
