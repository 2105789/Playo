<template>
    <div class="h-full flex flex-col p-1 mb-24">
      <h2 class="text-2xl font-bold mb-4">Trending Music</h2>
      <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4">
        <div v-for="video in trendingVideos" :key="video.id" class="card bg-base-200 shadow-md hover:bg-base-100 hover:scale-105">
          <figure>
            <img :src="video.snippet.thumbnails.medium.url" :alt="video.snippet.title" class="w-full h-48 object-cover" />
          </figure>
          <div class="card-body"  @click="playVideo(video)">
            <h3 class="card-title text-sm line-clamp-2">{{ video.snippet.title }}</h3>
            <p class="text-xs opacity-70">{{ video.snippet.channelTitle }}</p>
            <div class="card-actions justify-center mt-2">
              <button @click.stop="addToQueue(video)" class="btn btn-outline btn-sm">Add to Queue</button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  import { ref, onMounted } from 'vue';
  const awiks = '';
  const trendingVideos = ref([]);
  
  const emit = defineEmits(['video-selected', 'video-added']);
  
  const fetchTrendingVideos = async () => {
    const url = `https://www.googleapis.com/youtube/v3/videos?part=snippet&chart=mostPopular&videoCategoryId=10&maxResults=20&key=${awiks}`;
    
    try {
      const response = await fetch(url);
      const data = await response.json();
      trendingVideos.value = data.items;
    } catch (error) {
      console.error('Error fetching trending videos:', error);
    }
  };
  
  const formatVideoData = (video) => {
    return {
      id: { videoId: video.id },
      snippet: video.snippet,
    };
  };
  
  const playVideo = (video) => {
    emit('video-selected', formatVideoData(video), true);
  };
  
  const addToQueue = (video) => {
    emit('video-added', formatVideoData(video));
  };
  
  onMounted(() => {
    fetchTrendingVideos();
  });
  </script>
  
  <style scoped>
  .line-clamp-2 {
    display: -webkit-box;
    -webkit-line-clamp: 2;
    line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
  }
  </style>