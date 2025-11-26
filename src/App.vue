<script setup>
import { ref } from 'vue'
import Navbar from './components/Navbar.vue'
import Sidebar from './components/Sidebar.vue'
import TodoApp from './components/TodoApp.vue'

const activeContent = ref('')       // current main content

const sidebarOpen = ref(false)      // small screen sidebar toggle

function setContent(menu) {
  activeContent.value = menu
  sidebarOpen.value = false   // small screen e click korle sidebar auto hide
}
</script>

<template>
  <div class="h-screen flex flex-col">

    <!-- Navbar -->
    <Navbar @toggleSidebar="sidebarOpen = !sidebarOpen" />

    <!-- Content Area -->
    <div class="flex flex-1">

      <!-- Sidebar -->
      <Sidebar 
        :sidebarOpen="sidebarOpen" 

        @changeContent="setContent" 
      />

      <!-- Main Content -->
      <div 
        :class="['flex-1 bg-gray-100 p-6 overflow-auto min-h-screen transition-all duration-300', sidebarOpen ? 'ml-64' : 'ml-0', 'md:ml-64']"
      >

        <!-- Default message -->
        <div v-if="!activeContent" class="flex justify-center items-center h-full">
          <div class="text-center">
            <h2 class="text-3xl font-bold mb-4">Welcome!</h2>
            <p>Select a menu from the sidebar to see content</p>
          </div>
        </div>

        <!-- Todo content -->
        <div v-else-if="activeContent === 'Todo'" class="flex justify-center items-center h-full">
          <TodoApp />
        </div>

        <!-- Profile content -->
        <div v-else-if="activeContent === 'Profile'" class="flex justify-center items-center h-full">
          <div class="text-center">
            <h2 class="text-3xl font-bold mb-4">This is Profile Page</h2>
          </div>
        </div>

        <!-- Contact content -->
        <div v-else-if="activeContent === 'Contact'" class="flex justify-center items-center h-full">
          <div class="text-center">
            <h2 class="text-3xl font-bold mb-4">This is Contact Page</h2>
          </div>
        </div>

      </div>
    </div>
  </div>
</template>
