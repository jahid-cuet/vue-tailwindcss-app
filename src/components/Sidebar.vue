<script setup>
import { defineProps, defineEmits } from 'vue'

const emit = defineEmits(['changeContent'])
const props = defineProps({ sidebarOpen: Boolean })

function handleClick(menu) {
  emit('changeContent', menu)
}


</script>

<template>
  <aside 
    :class="[
      'bg-gray-800 text-white w-64 p-6 space-y-6 h-screen fixed top-0 left-0 shadow-lg transition-transform duration-300',
      props.sidebarOpen ? 'translate-x-0' : '-translate-x-full',
      'md:translate-x-0'
    ]"
  >
    <h2 class="text-2xl font-bold mb-6">Menu</h2>
    <ul class="space-y-4">
      <li>
        <button @click="handleClick('Profile')" class="cursor-pointer hover:text-blue-400 w-full text-left">Profile</button>
      </li>
      <li>
        <button @click="handleClick('Todo')" class= " cursor-pointer hover:text-blue-400 w-full text-left">Todo</button>
      </li>
      <li>
        <button @click="handleClick('Contact')" class="cursor-pointer hover:text-blue-400 w-full text-left">Contact</button>
      </li>
    </ul>
  </aside>
</template>
