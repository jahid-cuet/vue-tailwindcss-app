<script setup>
import { ref, onMounted } from "vue";

const props = defineProps({
  id: Number
});



const comments = ref([]);
const loading = ref(false);
const error = ref(null);

async function fetchcomments() {
  loading.value = true;
  error.value = null;

  try {
const response = await fetch(`https://jsonplaceholder.typicode.com/posts/${props.id}/comments`);
    if (!response.ok) throw new Error("Failed to fetch posts");
    comments.value = await response.json();
    console.log(JSON.stringify(comments.value, null, 2));

  } catch (err) {
    error.value = err.message;
    console.error("Error fetching Comments:", err);
  } finally {
    loading.value = false;
  }
}


onMounted(() => {
  fetchcomments();
});
</script>

<template>
  <div class="mx-auto mt-5 p-6 bg-white rounded-lg shadow-md w-full">

    <h1 class="text-2xl font-bold mb-4">Comments of this Post</h1>

    <div v-if="loading" class="text-blue-600 text-center">Loading...</div>
    <div v-if="error" class="text-red-600 text-center">Error: {{ error }}</div>

    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
      <div 
        v-for="c in comments" 
        :key="c.id" 
        class="bg-white p-4 rounded-xl shadow-md hover:shadow-lg transition-shadow duration-300"
      >
        <!-- Name -->
        <h3 class="text-lg font-semibold mb-2 text-gray-800">
          Name : {{ c.name.split(' ').slice(0, 3).join(' ') }}{{ c.name.split(' ').length > 3 ? '...' : '' }}
        </h3>

        <!-- Email -->
        <p class="text-sm text-gray-500 mb-3"> Email : {{ c.email }}</p>

        <!-- Body -->
        <p class="text-gray-700">
         Body:  {{ c.body.split(' ').slice(0, 5).join(' ') }}{{ c.body.split(' ').length > 5 ? '...' : '' }}
        </p>
      </div>
    </div>

    <div v-if="!loading && comments.length === 0" class="text-gray-500 text-center mt-6">
      No comments found.
    </div>
  </div>
</template>

