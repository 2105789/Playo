/compoenet/search.vue
<template>
  <div class="h-full flex flex-col p-1">
    <div class="mb-6 relative">
      <input
        type="text"
        id="searchInput"
        placeholder="Search Music"
        v-model="searchQuery"
        @keyup.enter="searchVideos"
        class="input input-bordered w-full"
      />
      <button
        @click="clearSearch"
        v-if="searchQuery"
        class="btn btn-ghost btn-circle absolute right-14 top-1/2 transform -translate-y-1/2"
      >
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
          <path fill-rule="evenodd" d="M10 18a8 8 0 100-16 8 8 0 000 16zM8.707 7.293a1 1 0 00-1.414 1.414L8.586 10l-1.293 1.293a1 1 0 101.414 1.414L10 11.414l1.293 1.293a1 1 0 001.414-1.414L11.414 10l1.293-1.293a1 1 0 00-1.414-1.414L10 8.586 8.707 7.293z" clip-rule="evenodd" />
        </svg>
      </button>
      <button
        @click="searchVideos"
        class="btn btn-primary absolute right-0 top-0 rounded-l-none"
      >
        <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
          <path fill-rule="evenodd" d="M8 4a4 4 0 100 8 4 4 0 000-8zM2 8a6 6 0 1110.89 3.476l4.817 4.817a1 1 0 01-1.414 1.414l-4.816-4.816A6 6 0 012 8z" clip-rule="evenodd" />
        </svg>
      </button>
    </div>

    <div class="flex-grow overflow-y-auto mb-24">
      <div v-if="isLoading" class="flex justify-center items-center h-full">
        <span class="loading loading-spinner loading-lg"></span>
      </div>
      <div v-else-if="searchResults.length" class="space-y-4">
        <div
          v-for="result in searchResults"
          :key="result.id.videoId"
          class="card bg-base-100 shadow-md"
        >
          <div class="card-body p-4" @click="selectVideo(result, true)">
            <div class="flex items-center">
              <img
                :src="result.snippet.thumbnails.default.url"
                :alt="result.snippet.title"
                class="w-16 h-15 md:h-18 md:w-20 object-cover rounded-lg"
              />
              <div class="ml-4 flex-grow">
                <h3 class="card-title text-sm mb-1 line-clamp-1 w-28 md:w-full">
                  {{ result.snippet.title }}
                </h3>
                <p class="text-xs opacity-70 line-clamp-1 w-28 md:w-full">
                  {{ result.snippet.description }}
                </p>
              </div>
              <div class="flex flex-col space-y-2 z-10 w-24 md:w-32">
                <button
                  @click.stop="selectVideo(result, false)"
                  class="btn btn-sm btn-outline btn-success"
                >
                  Add to Queue
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div
        v-else-if="hasSearched"
        class="flex justify-center items-center h-full text-base-content opacity-50"
      >
        No results found. Try a different search term.
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
const awiks = '';
const searchQuery = ref('');
const searchResults = ref([]);
const isLoading = ref(false);
const hasSearched = ref(false);

const searchVideos = async () => {
  if (!searchQuery.value.trim()) return;

  isLoading.value = true;
  hasSearched.value = true;
  const url = `https://www.googleapis.com/youtube/v3/search?part=snippet&maxResults=15&q=${encodeURIComponent(
    searchQuery.value
  )}&type=video&videoCategoryId=10&key=${awiks}`;

  try {
    const response = await fetch(url);
    const data = await response.json();
    searchResults.value = data.items;
  } catch (error) {
    console.error('Error fetching search results:', error);
  } finally {
    isLoading.value = false;
  }
};

const clearSearch = () => {
  searchQuery.value = '';
  searchResults.value = [];
  hasSearched.value = false;
};

const selectVideo = (video, playImmediately) => {
  if (playImmediately) {
    emit('video-selected', video, true);
  } else {
    emit('video-added', video);
  }
};

const emit = defineEmits(['video-selected', 'video-added']);
</script>

<style scoped>
.line-clamp-1 {
  display: -webkit-box;
  -webkit-line-clamp: 1;
  line-clamp: 1;
  -webkit-box-orient: vertical;
  overflow: hidden;
}
</style>