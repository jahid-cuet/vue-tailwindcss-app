<script setup>
import { ref, onMounted } from "vue";


// create start

const showCreateForm = ref(false);
const newPost = ref({
  title: "",
  body: ""
});



function toggleCreateForm() {
  showCreateForm.value = !showCreateForm.value;
}

function createPost() {
  if (!newPost.value.title || !newPost.value.body) return;

  const newId = posts.value.length
    ? posts.value[posts.value.length - 1].id + 1
    : 1;

  posts.value.unshift({
    id: newId,
    title: newPost.value.title,
    body: newPost.value.body
  });

  // Reset form and hide again
  newPost.value = { title: "", body: "" };
  showCreateForm.value = false;
}


//Create End

const emit = defineEmits(["showPost"]); // send event + id to parent

const posts = ref([]);
const loading = ref(false);
const error = ref(null);

// Modal state For Delete
const showModal = ref(false);
const deleteId = ref(null);

// For Edit
const showEditModal = ref(false);
const editPostData = ref({ id: null, title: "", body: "" });

// Fetch posts from JSONPlaceholder
async function fetchPosts() {
  loading.value = true;
  error.value = null;

  try {
    const response = await fetch(
      "https://jsonplaceholder.typicode.com/posts?_limit=10"
    );
    if (!response.ok) throw new Error("Failed to fetch posts");
    posts.value = await response.json();
  } catch (err) {
    error.value = err.message;
    console.error("Error fetching posts:", err);
  } finally {
    loading.value = false;
  }
}

function openDeleteModal(id) {
  deleteId.value = id;
  showModal.value = true;
}

function confirmDelete() {
  posts.value = posts.value.filter((p) => p.id !== deleteId.value);
  showModal.value = false;
  deleteId.value = null;
}

function cancelDelete() {
  showModal.value = false;
  deleteId.value = null;
}

// For Show Post
function showPost(id) {
  emit("showPost", "ShowPost", id);
}

// Edit

function openEditModal(post) {
  editPostData.value = { ...post }; // copy post data
  showEditModal.value = true;
}

function saveEdit() {
  // Find the post and update
  const index = posts.value.findIndex((p) => p.id === editPostData.value.id);
  if (index !== -1) {
    posts.value[index] = { ...editPostData.value };
  }

  closeEditModal();
}

function closeEditModal() {
  showEditModal.value = false;
  editPostData.value = { id: null, title: "", body: "" };
}

onMounted(() => {
  fetchPosts();
});
</script>

<template>
  <div class="mx-auto mt-5 p-6 bg-white rounded-lg shadow-md w-full">
    <h1 class="text-2xl font-bold mb-4">Posts List</h1>


<!-- Create Post start -->

 <!-- Add Post Button -->
<button
  @click="toggleCreateForm"
  class="px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700 mb-4"
>
  {{ showCreateForm ? "Close" : "Add Post" }}
</button>

<!-- Create Post Form (toggle) -->
<div v-if="showCreateForm" class="mb-6 p-4 bg-blue-50 rounded shadow">
  <h2 class="text-xl font-bold mb-3">Create New Post</h2>

  <input
    v-model="newPost.title"
    type="text"
    placeholder="Post title"
    class="w-full border px-3 py-2 rounded mb-2"
  />

  <textarea
    v-model="newPost.body"
    placeholder="Post body"
    rows="3"
    class="w-full border px-3 py-2 rounded mb-3"
  />

  <button
    @click="createPost"
    class="px-4 py-2 bg-green-600 text-white rounded hover:bg-green-700"
  >
    Save Post
  </button>
</div>

<!-- Create Post End -->









    <div v-if="loading" class="text-blue-600">Loading...</div>
    <div v-if="error" class="text-red-600">Error: {{ error }}</div>

    <div v-if="!loading && posts.length" class="overflow-x-auto mt-4">
      <table
        class="w-full max-w-5xl mx-auto bg-purple-100 border border-gray-200 rounded-lg shadow-sm text-sm"
      >
        <thead class="bg-gray-300 border-b">
          <tr>
            <th class="py-2 px-4 text-left">ID</th>
            <th class="py-2 px-4 text-left">Title</th>
            <th class="py-2 px-4 text-left">Body</th>
            <th class="py-2 px-4 text-left">Show</th>
            <th class="py-2 px-4 text-left">Edit</th>
            <th class="py-2 px-4 text-left">Delete</th>
          </tr>
        </thead>

        <tbody>
          <tr v-for="post in posts" :key="post.id" class="border-b">
            <td class="py-2 px-4">{{ post.id }}</td>

            <td class="py-2 px-4">
              {{ post.title.split(" ").slice(0, 3).join(" ") }}
              {{ post.title.split(" ").length > 3 ? "..." : "" }}
            </td>

            <td class="py-2 px-4">
              {{ post.body.split(" ").slice(0, 5).join(" ") }}
              {{ post.body.split(" ").length > 5 ? "..." : "" }}
            </td>

            <!-- SHOW POST -->
            <td class="py-2 px-4 items-center text-red-500">
              <button
                @click="showPost(post.id)"
                class="inline-flex items-center p-1"
              >
                <svg
                  xmlns="http://www.w3.org/2000/svg"
                  class="h-5 w-5 text-blue-600 block cursor-pointer hover:text-blue-700 transition-colors duration-200"
                  fill="none"
                  viewBox="0 0 24 24"
                  stroke="currentColor"
                >
                  <!-- Pupil -->
                  <circle
                    cx="12"
                    cy="12"
                    r="3"
                    stroke="currentColor"
                    stroke-width="2"
                    fill="none"
                  />
                  <!-- Eye outline -->
                  <path
                    stroke="currentColor"
                    stroke-width="2"
                    stroke-linecap="round"
                    stroke-linejoin="round"
                    d="M12 5c-7 0-10 7-10 7s3 7 10 7 10-7 10-7-3-7-10-7z"
                  />
                </svg>
              </button>
            </td>

            <td class="py-2 px-4 text-center">
              <button @click="openEditModal(post)" class="cursor-pointer">
                ✏️
              </button>
            </td>

            <td class="py-2 px-4 items-center text-red-500">
              <button @click="openDeleteModal(post.id)">
                <svg
                  class="cursor-pointer"
                  width="24"
                  height="24"
                  viewBox="0 0 24 24"
                  fill="none"
                  stroke="currentColor"
                  stroke-width="2"
                  stroke-linecap="round"
                  stroke-linejoin="round"
                >
                  <polyline points="3 6 5 6 21 6"></polyline>
                  <path
                    d="M19 6v14a2 2 0 0 1-2 2H7a2 2 0 0 1-2-2V6m3 0V4a2 2 0 0 1 2-2h4a2 2 0 0 1 2 2v2"
                  ></path>
                  <line x1="10" y1="11" x2="10" y2="17"></line>
                  <line x1="14" y1="11" x2="14" y2="17"></line>
                </svg>
              </button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>

    <div v-if="!loading && posts.length === 0" class="text-gray-500">
      No posts found.
    </div>

    <!-- Delete Confirmation Modal -->
    <div
      v-if="showModal"
      class="fixed inset-0 flex items-center justify-center z-50 pointer-events-none"
    >
      <!-- Modal content -->
      <div
        class="bg-white p-6 rounded-lg shadow-lg z-10 pointer-events-auto w-80"
      >
        <h3 class="text-xl font-bold mb-4">Confirm Delete</h3>
        <p class="mb-6">Are you sure you want to delete this post?</p>
        <div class="flex justify-end gap-4">
          <button
            @click="cancelDelete"
            class="cursor-pointer px-4 py-2 rounded bg-gray-200 hover:bg-gray-300"
          >
            Cancel
          </button>
          <button
            @click="confirmDelete"
            class="cursor-pointer px-4 py-2 rounded bg-red-500 text-white hover:bg-red-600"
          >
            Delete
          </button>
        </div>
      </div>
    </div>

<!-- Edit Modal -->
<div
  v-if="showEditModal"
  class="fixed inset-0 flex items-center justify-center z-50 pointer-events-none"
>
  <div class="bg-white p-6 rounded-lg shadow-lg z-10 w-96 pointer-events-auto">
    <h3 class="text-xl font-bold mb-4">Edit Post</h3>

    <div class="mb-4">
      <label class="block mb-1 font-medium">Title</label>
      <input v-model="editPostData.title" type="text"
        class="w-full border border-gray-300 rounded px-3 py-2" />
    </div>

    <div class="mb-6">
      <label class="block mb-1 font-medium">Body</label>
      <textarea v-model="editPostData.body" rows="4"
        class="w-full border border-gray-300 rounded px-3 py-2"></textarea>
    </div>

    <div class="flex justify-end gap-4">
      <button @click="closeEditModal"
        class="cursor-pointer px-4 py-2 rounded bg-gray-200 hover:bg-gray-300">
        Cancel
      </button>

      <button @click="saveEdit"
        class="cursor-pointer px-4 py-2 rounded bg-green-500 text-white hover:bg-green-600">
        Save
      </button>
    </div>
  </div>
</div>

  </div>
</template>
