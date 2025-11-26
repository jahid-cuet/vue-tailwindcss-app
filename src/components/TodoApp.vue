<script setup>
import { ref, watch } from "vue";

const newTodo = ref("");
const todos = ref([]);

// Load todos from localStorage
try {
  const savedTodos = localStorage.getItem("todos");
  todos.value = savedTodos ? JSON.parse(savedTodos) : [];
} catch (e) {
  console.error("Error loading todos from localStorage:", e);
  todos.value = [];
}

// Save todos to localStorage when changed
watch(
  todos,
  (newVal) => {
    localStorage.setItem("todos", JSON.stringify(newVal));
  },
  { deep: true }
);

// Add new todo
function addTodo() {
  if (newTodo.value.trim() === "") return;
  todos.value.push({
    id: Date.now(),
    text: newTodo.value,
    completed: false,
  });
  newTodo.value = "";
}

// Toggle complete
function toggleTodo(todo) {
  todo.completed = !todo.completed;
}

// Delete todo
function deleteTodo(id) {
  todos.value = todos.value.filter((todo) => todo.id !== id);
}

// Edit todo
function editTodo(todo) {
  const updated = prompt("Edit todo", todo.text);
  if (updated !== null && updated.trim() !== "") {
    todo.text = updated;
  }
}
</script>

<template>
  <div class="max-w-md mx-auto mt-5 p-6 bg-white rounded-lg shadow-md">

    <h1 class="text-2xl font-bold mb-4">My Todo List</h1>

    <!-- Input Section -->
    <div class="flex gap-2 mb-4">
      <input
        v-model="newTodo"
        placeholder="Add new todo"
        class="flex-1 border border-gray-300 rounded px-3 py-2 focus:outline-none focus:ring-2 focus:ring-blue-400"
      />
      <button @click="addTodo" class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
        Add
      </button>
    </div>

    <!-- Todo List -->
    <ul class="space-y-2">
      <li
        v-for="todo in todos"
        :key="todo.id"
        class="flex justify-between items-center bg-gray-100 px-3 py-2 rounded"
      >
        <span 
          @click="toggleTodo(todo)" 
          :class="{ 'line-through text-gray-400': todo.completed }"
          class="cursor-pointer"
        >
          {{ todo.text }}
        </span>
        <div class="flex gap-2">
          <button @click="editTodo(todo)" class="bg-yellow-400 px-2 py-1 rounded hover:bg-yellow-500 text-sm">
            Edit
          </button>
          <button @click="deleteTodo(todo.id)" class="bg-red-500 px-2 py-1 rounded hover:bg-red-600 text-sm text-white">
            Delete
          </button>
        </div>
      </li>
    </ul>

  </div>
</template>
