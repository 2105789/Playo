<template>
  <div class="h-screen flex flex-col bg-base-200 text-base-content"> 
    <div class="flex-grow relative overflow-hidden flex flex-col"> 
      <div class="flex justify-center mb-4 sm:mb-4 pt-4">
        <div class="tabs tabs-boxed border-solid border-2 border-primary">
          <a class="tab tab-sm sm:tab-md" :class="{ 'tab-active': activeTab === 'home' }" @click="activeTab = 'home'">Home</a>
          <a class="tab tab-sm sm:tab-md" :class="{ 'tab-active': activeTab === 'search' }" @click="activeTab = 'search'">Search</a>
          <a class="tab tab-sm sm:tab-md" :class="{ 'tab-active': activeTab === 'queue' }" @click="activeTab = 'queue'">Queue</a>
        </div>
      </div>
      <div class="flex-grow overflow-y-auto"> <div class="p-2 sm:p-4 md:p-6"> 
          <transition name="fade" mode="out-in">
            <div> 
              <Home v-if="activeTab === 'home'" @video-selected="handleVideoSelect" @video-added="addToQueue" />
              <Search v-if="activeTab === 'search'" @video-selected="handleVideoSelect" @video-added="addToQueue" />
              <Queue v-if="activeTab === 'queue'" :queue="videoQueue" @video-selected="playVideo" @remove-from-queue="removeFromQueue" @clear-queue="clearQueue" />
            </div> 
          </transition>
        </div> 
      </div>
      <div class="z-30 absolute md:top-6 md:right-6 flex items-center justify-between top-4 right-4">
        <div class="dropdown dropdown-bottom dropdown-end">
          <button tabindex="0" class="h-8 w-8 border-solid border-2 border-primary rounded-full flex justify-center items-center">
            <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24"><path fill="currentColor" d="M7.5 2c-1.79 1.15-3 3.18-3 5.5s1.21 4.35 3.03 5.5C4.46 13 2 10.54 2 7.5A5.5 5.5 0 0 1 7.5 2m11.57 1.5l1.43 1.43L4.93 20.5L3.5 19.07zm-6.18 2.43L11.41 5L9.97 6l.42-1.7L9 3.24l1.75-.12l.58-1.65L12 3.1l1.73.03l-1.35 1.13zm-3.3 3.61l-1.16-.73l-1.12.78l.34-1.32l-1.09-.83l1.36-.09l.45-1.29l.51 1.27l1.36.03l-1.05.87zM19 13.5a5.5 5.5 0 0 1-5.5 5.5c-1.22 0-2.35-.4-3.26-1.07l7.69-7.69c.67.91 1.07 2.04 1.07 3.26m-4.4 6.58l2.77-1.15l-.24 3.35zm4.33-2.7l1.15-2.77l2.2 2.54zm1.15-4.96l-1.14-2.78l3.34.24zM9.63 18.93l2.77 1.15l-2.53 2.19z"/></svg>
          </button>
          <ul tabindex="0" class="dropdown-content menu bg-base-100 rounded-box z-[1] p-2 shadow overflow-y-auto h-96 w-80"> 
            <li v-for="(theme, index) in availableThemes" :key="index">
              <a @click="selectedTheme = theme; saveTheme()">{{ theme }}</a>
            </li>
          </ul>
        </div>
      </div>
    </div>
    <Player ref="playerComponent" :current-video="currentVideo" :queue="videoQueue" :loop-queue="loopQueue" @video-ended="playNextVideo" @previous-track="playPreviousVideo" @next-track="playNextVideo" />
  </div>
</template>

<script setup>
import { ref, computed, onMounted, watch } from 'vue';


const activeTab = ref('home');
const showSearch = ref(true);
const videoQueue = ref([]);
const currentVideoIndex = ref(-1);
const playerComponent = ref(null);
const loopQueue = ref(false);
const selectedTheme = ref('light');
const availableThemes = [
  "light",
  "dark",
  "cupcake",
  "bumblebee",
  "emerald",
  "corporate",
  "synthwave",
  "retro",
  "cyberpunk",
  "valentine",
  "halloween",
  "garden",
  "forest",
  "aqua",
  "lofi",
  "pastel",
  "fantasy",
  "wireframe",
  "black",
  "luxury",
  "dracula",
  "cmyk",
  "autumn",
  "business",
  "acid",
  "lemonade",
  "night",
  "coffee",
  "winter",
  "dim",
  "nord",
  "sunset",
];

const currentVideo = computed(() => {
  return currentVideoIndex.value >= 0 ? videoQueue.value[currentVideoIndex.value] : null;
});

const addToQueue = (video) => {
  videoQueue.value.push(video);
  if (videoQueue.value.length === 1) {
    playVideo(video);
  }
};

const playVideo = (video) => {
  const index = videoQueue.value.findIndex((v) => v.id.videoId === video.id.videoId);
  if (index !== -1) {
    currentVideoIndex.value = index;
    playerComponent.value.loadVideo(video.id.videoId);
  }
};

const playNextVideo = () => {
  if (currentVideoIndex.value < videoQueue.value.length - 1) {
    currentVideoIndex.value++;
  } else if (loopQueue.value && videoQueue.value.length > 0) {
    currentVideoIndex.value = 0;
  } else {
    return;
  }
  playerComponent.value.loadVideo(videoQueue.value[currentVideoIndex.value].id.videoId);
};

const playPreviousVideo = () => {
  if (currentVideoIndex.value > 0) {
    currentVideoIndex.value--;
  } else if (loopQueue.value && videoQueue.value.length > 0) {
    currentVideoIndex.value = videoQueue.value.length - 1;
  } else {
    return;
  }
  playerComponent.value.loadVideo(videoQueue.value[currentVideoIndex.value].id.videoId);
};

const clearQueue = () => {
  videoQueue.value = [];
  currentVideoIndex.value = -1;
  playerComponent.value.stopVideo();
};

const removeFromQueue = (index) => {
  if (index === currentVideoIndex.value) {
    playNextVideo();
  } else if (index < currentVideoIndex.value) {
    currentVideoIndex.value--;
  }
  videoQueue.value.splice(index, 1);
};

const handleVideoSelect = (video, playImmediately) => {
  if (playImmediately) {
    videoQueue.value.unshift(video);
    currentVideoIndex.value = 0;
    playVideo(video);
  } else {
    addToQueue(video);
  }
};

const saveTheme = () => {
  if (typeof window !== 'undefined') {
    window.localStorage.setItem('selectedTheme', selectedTheme.value);
    document.documentElement.setAttribute('data-theme', selectedTheme.value);
  }
};

onMounted(() => {
  if (typeof window !== 'undefined') {
    const savedTheme = window.localStorage.getItem('selectedTheme') || 'light';
    if (savedTheme) {
      selectedTheme.value = savedTheme;
      document.documentElement.setAttribute('data-theme', savedTheme); 
    }
  }
});

watch(videoQueue, (newQueue) => {
  if (newQueue.length === 0) {
    currentVideoIndex.value = -1;
  }
}, { deep: true });
</script>