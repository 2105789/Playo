<template>
  <div class="h-full flex flex-col">
    <div class="p-2 z-10 card bg-base-100 mt-1 mb-2 sm:mb-4">
      <div class="card-body p-2 flex flex-row items-center justify-between">
        <h3 class="card-title text-sm sm:text-base">Queue</h3>
        <button
          @click="clearQueue"
          class="btn btn-xs sm:btn-sm btn-secondary"
        >
          Clear Queue
        </button>
      </div>
    </div>
    <div class="flex-grow overflow-y-auto mb-24 rounded-xl">
      <div v-if="queue.length" class="space-y-2 sm:space-y-4">
        <div
          v-for="(video, index) in queue"
          :key="video.id.videoId"
          class="card bg-base-100 shadow-md"
        >
          <div class="card-body p-2 sm:p-4">
            <div class="flex items-center">
              <span class="text-xs sm:text-sm opacity-50 w-4 sm:w-6 text-center">{{ index + 1 }}.</span>
              <img
                :src="video.snippet.thumbnails.default.url"
                :alt="video.snippet.title"
                class="w-16 h-12 sm:w-20 sm:h-15 object-cover rounded-lg"
              />
              <div class="ml-2 sm:ml-4 flex-grow">
                <h3 class="card-title text-xs sm:text-sm mb-1 line-clamp-1">
                  {{ video.snippet.title }}
                </h3>
                <p class="text-xs opacity-70 line-clamp-1 hidden sm:block">
                  {{ video.snippet.description }}
                </p>
              </div>
              <div class="flex flex-col sm:flex-row space-y-1 sm:space-y-0 sm:space-x-2">
                <button
                  @click="selectVideo(video)"
                  class="btn btn-xs sm:btn-sm btn-primary"
                >
                  Play
                </button>
                <button
                  @click="removeVideo(index)"
                  class="btn btn-xs sm:btn-sm btn-secondary"
                >
                  Remove
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div v-else class="flex justify-center items-center h-full text-base-content opacity-50 text-sm sm:text-base">
        Your queue is empty.
      </div>
    </div>
  </div>
</template>


<script setup>
const props = defineProps({
  queue: {
    type: Array,
    required: true,
  },
});

const emit = defineEmits(['video-selected', 'remove-from-queue', 'clear-queue']);

const clearQueue = () => {
  emit('clear-queue');
};

const selectVideo = (video) => {
  emit('video-selected', video);
};

const removeVideo = (index) => {
  emit('remove-from-queue', index);
};
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