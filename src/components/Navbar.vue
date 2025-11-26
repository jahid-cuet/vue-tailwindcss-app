<script setup>
import { ref, defineEmits } from 'vue'

const emit = defineEmits(['toggleSidebar'])

const isOpen = ref(false)

function toggleDropdown(){
  isOpen.value = !isOpen.value
}
</script>

<template>
  <nav class="bg-gray-900 text-white px-6 py-4 flex justify-between items-center fixed w-full top-0 z-50 shadow">
    
    <!-- Hamburger Button (small screen) -->
    <button @click="emit('toggleSidebar')" class="md:hidden text-2xl">
      ☰
    </button>

    <ul class="flex gap-8 text-lg ml-auto">
      <li><a href="" class="hover:text-blue-400 transition duration-200">Home</a></li>

      <!-- DropDown -->
      <li class ="relative">
        <button @click="toggleDropdown" class="hover:text-blue-400 transition duration-200">
          DropDown
        </button>
        <ul v-if="isOpen" class="absolute right-0 mt-2 w-40 bg-white text-black rounded shadow-lg">
          <li><a href="" class="block px-4 py-2 hover:bg-gray-200">Option 1</a></li>
          <li><a href="" class="block px-4 py-2 hover:bg-gray-200">Option 2</a></li>
          <li><a href="" class="block px-4 py-2 hover:bg-gray-200">Option 3</a></li>
        </ul>
      </li>

      <li><a href="" class="hover:text-blue-400 transition duration-200">User</a></li>
    </ul>
  </nav>
</template>
