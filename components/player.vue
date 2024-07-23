<template>
  <div class="z-20 fixed bottom-0 left-0 w-full bg-base-200 text-base-content shadow-lg transition-all duration-300 ease-in-out border-solid border-t-4 border-primary border-opacity-10">
    <div class="container mx-auto py-2 sm:py-4">
      <div class="flex flex-col sm:flex-row items-center justify-between   px-6 md:px-0">
        <div class="flex items-center space-x-2 sm:space-x-4 w-full sm:w-1/3 mb-2 sm:mb-0">
          <img v-if="currentVideo?.snippet?.thumbnails?.default?.url"
            :src="currentVideo?.snippet?.thumbnails?.default?.url"
            alt="Video Thumbnail"
            class="w-16 h-12 sm:w-20 sm:h-16 rounded-lg shadow-md object-cover"
          />
          <div v-else class="skeleton h-12 w-16 sm:h-16 sm:w-20"></div>
          <div class="flex flex-col">
            <p
              class="font-semibold text-sm sm:text-base line-clamp-1"
              :title="currentVideo?.snippet?.title"
            >
              {{ currentVideo?.snippet?.title }}
            </p>
            <p class="text-xs sm:text-sm opacity-70">
              {{ formatTime(currentTime) }} / {{ formatTime(duration) }}
            </p>
          </div>
        </div>

        <div class="flex flex-col items-center space-y-2 w-full sm:w-1/3">
          <div class="flex items-center space-x-4 sm:space-x-6">
            <button
              @click="playPreviousTrack"
              class="btn btn-sm sm:btn-md btn-circle btn-ghost"
            >
              <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <path d="m12 19-7-7 7-7" />
                <path d="M19 12H5" />
              </svg>
            </button>
            <button
              @click="playPause"
              class="btn btn-sm sm:btn-md btn-circle btn-primary"
            >
              <svg v-if="isPlaying" xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <rect width="4" height="16" x="6" y="4" />
                <rect width="4" height="16" x="14" y="4" />
              </svg>
              <svg v-else xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <polygon points="5 3 19 12 5 21 5 3" />
              </svg>
            </button>
            <button
              @click="playNextTrack"
              class="btn btn-sm sm:btn-md btn-circle btn-ghost"
            >
              <svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                <path d="m12 5 7 7-7 7" />
                <path d="M5 12h14" />
              </svg>
            </button>
          </div>
          <input
            type="range"
            min="0"
            :max="duration"
            :value="currentTime"
            @input="handleSeek"
            class="range range-xs sm:range-sm range-primary w-full"
          />
        </div>

        <div class="flex items-center space-x-2 sm:space-x-4 w-full sm:w-1/3 justify-center md:justify-end mt-2 sm:mt-0">
          <input
            type="range"
            min="0"
            max="100"
            v-model="volume"
            @input="handleVolumeChange"
            class="range range-xs sm:range-sm range-primary w-16 sm:w-24"
          />
          <button
            @click="toggleMute"
            class="btn btn-sm sm:btn-md btn-circle btn-ghost"
          >
            <svg 
              v-if="isMuted" 
              xmlns="http://www.w3.org/2000/svg" 
              width="20" 
              height="20" 
              viewBox="0 0 20 20">
              <g fill="currentColor"><path fill-rule="evenodd" d="m10.334 1.754l-4.68 4.178H2a1 1 0 0 0-1 1v6a1 1 0 0 0 1 1h3.535l4.796 4.312c.644.578 1.669.122 1.669-.744v-15c0-.864-1.021-1.321-1.666-.746M6.666 7.709L10 4.733v10.523l-3.126-2.81A1 1 0 0 0 6 11.932H3v-4h2.751c.302.079.642.02.915-.223" clip-rule="evenodd"/><path d="M15.489 13.069a.75.75 0 1 1-.978-1.138c.034-.029.067-.06.1-.094c.138-.143.261-.324.362-.536A3.05 3.05 0 0 0 15.25 10c0-.754-.25-1.433-.639-1.837a1.488 1.488 0 0 0-.1-.094a.75.75 0 1 1 .978-1.138c.07.06.137.124.202.191c.67.696 1.059 1.75 1.059 2.878c0 .696-.147 1.366-.423 1.945c-.168.355-.383.67-.636.933a2.942 2.942 0 0 1-.202.19"/><path d="M1.293 2.707a1 1 0 0 1 1.414-1.414l16 16a1 1 0 0 1-1.414 1.414z"/></g></svg>
              <svg 
              v-else
              xmlns="http://www.w3.org/2000/svg" 
              width="20" 
              height="20" 
              viewBox="0 0 20 20"><g fill="currentColor"><path fill-rule="evenodd" d="m10.334 1.754l-4.68 4.178H2a1 1 0 0 0-1 1v6a1 1 0 0 0 1 1h3.535l4.796 4.312c.644.578 1.669.122 1.669-.744v-15c0-.864-1.021-1.321-1.666-.746M6.666 7.709L10 4.733v10.523l-3.126-2.81A1 1 0 0 0 6 11.932H3v-4h2.751c.302.079.642.02.915-.223" clip-rule="evenodd"/><path d="M15.489 13.069a.75.75 0 1 1-.978-1.138c.034-.029.067-.06.1-.094c.138-.143.261-.324.362-.536A3.05 3.05 0 0 0 15.25 10c0-.754-.25-1.433-.639-1.837a1.488 1.488 0 0 0-.1-.094a.75.75 0 1 1 .978-1.138c.07.06.137.124.202.191c.67.696 1.059 1.75 1.059 2.878c0 .696-.147 1.366-.423 1.945c-.168.355-.383.67-.636.933a2.942 2.942 0 0 1-.202.19"/><path d="M16.411 16.127a.75.75 0 0 1-.822-1.254a6.032 6.032 0 0 0 1.304-1.16a5.91 5.91 0 0 0 .904-1.474a5.72 5.72 0 0 0 .425-2.813a5.732 5.732 0 0 0-1.329-3.14a5.968 5.968 0 0 0-1.304-1.159a.75.75 0 1 1 .822-1.254a7.51 7.51 0 0 1 2.071 2.03a7.32 7.32 0 0 1 .945 1.955a7.21 7.21 0 0 1 .287 2.865a7.248 7.248 0 0 1-2.166 4.49a7.515 7.515 0 0 1-1.137.914"/></g>
              </svg>
          </button>
          <button
            @click="toggleVideoVisibility"
            class="btn btn-sm sm:btn-md btn-circle btn-ghost"
          >

            <svg  v-if="isVideoVisible"
              xmlns="http://www.w3.org/2000/svg"
              width="20"
              height="20"
              viewBox="0 0 24 24"
              fill="none"
              stroke="currentColor"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round">
              <path d="M9.88 9.88a3 3 0 1 0 4.24 4.24" />
              <path d="M10.73 5.08A10.43 10.43 0 0 1 12 5c7 0 10 7 10 7a13.16 13.16 0 0 1-1.67 2.68" />
              <path d="M6.61 6.61A13.526 13.526 0 0 0 2 12s3 7 10 7a9.74 9.74 0 0 0 5.39-1.61" />
              <line x1="2" y1="2" x2="22" y2="22" />
            </svg>
            <svg
              v-else
              xmlns="http://www.w3.org/2000/svg" 
              width="20" 
              height="20" 
              viewBox="0 0 24 24"><path fill="currentColor" d="M10.5 17q1.05 0 1.775-.725T13 14.5V9h2q.425 0 .713-.288T16 8t-.288-.712T15 7h-2q-.425 0-.712.288T12 8v4.5q-.325-.225-.7-.363T10.5 12q-1.05 0-1.775.725T8 14.5t.725 1.775T10.5 17M4 20q-.825 0-1.412-.587T2 18V6q0-.825.588-1.412T4 4h16q.825 0 1.413.588T22 6v12q0 .825-.587 1.413T20 20zm0-2h16V6H4zm0 0V6z"/>
            </svg>
          </button>
        </div>
      </div>
    </div>
    
    <div 
      :class="['transition-all duration-300 ease-in-out', isVideoVisible ? 'h-48 sm:h-72 md:h-[360px]' : 'h-0']"
      class="w-full overflow-hidden relative"
    >
      <div 
        ref="playerRef" 
        class="w-full h-full"
      ></div>
      <div 
        class="absolute top-0 left-0 w-full h-full cursor-pointer"
        @click="playPause"
      ></div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, watch, nextTick } from "vue";

const props = defineProps({
  currentVideo: {
    type: Object,
    default: () => ({}),
  },
  queue: {
    type: Array,
    default: () => [],
  },
  loopQueue: {
    type: Boolean,
    default: false,
  },
});

const playerRef = ref(null);
const player = ref(null);
const isPlaying = ref(false);
const isMuted = ref(false);
const currentTime = ref(0);
const duration = ref(0);
const volume = ref(100);
const isVideoVisible = ref(false);

const emit = defineEmits(["video-ended", "previous-track", "next-track"]);

const formatTime = (time) => {
  const minutes = Math.floor(time / 60);
  const seconds = Math.floor(time % 60);
  return `${minutes}:${seconds.toString().padStart(2, "0")}`;
};

const createPlayer = () => {
  if (!window.YT) {
    const tag = document.createElement("script");
    tag.src = "https://www.youtube.com/iframe_api";
    const firstScriptTag = document.getElementsByTagName("script")[0];
    firstScriptTag.parentNode.insertBefore(tag, firstScriptTag);
    window.onYouTubeIframeAPIReady = createPlayer;
  } else {
    player.value = new YT.Player(playerRef.value, {
      height: "100%",
      width: "100%",
      videoId: "",
      playerVars: {
        playsinline: 1,
        controls: 0,
        disablekb: 1,
        modestbranding: 1,
        rel: 0,
        showinfo: 0,
        iv_load_policy: 3,
      },
      events: {
        onReady: onPlayerReady,
        onStateChange: onPlayerStateChange,
        onError: onPlayerError,
      },
    });
  }
};

onMounted(() => {
  createPlayer();
});

const onPlayerReady = () => {
  isPlaying.value = player.value.getPlayerState() === YT.PlayerState.PLAYING;
  setInterval(updateTimeAndProgress, 1000);
};

const onPlayerStateChange = (event) => {
  if (player.value) {
    isPlaying.value = event.data === YT.PlayerState.PLAYING;
  }
  if (event.data === YT.PlayerState.ENDED) {
    emit("video-ended");
  }
};

const onPlayerError = (event) => {
  console.error("Player error:", event.data);
};

const updateTimeAndProgress = () => {
  if (player.value && player.value.getCurrentTime) {
    currentTime.value = player.value.getCurrentTime();
    duration.value = player.value.getDuration();
  }
};

const loadVideo = (videoId) => {
  if (player.value) {
    player.value.loadVideoById(videoId);
    nextTick(() => {
      isPlaying.value = player.value.getPlayerState() === YT.PlayerState.PLAYING;
    });
  }
};

const playPause = () => {
  if (player.value) {
    if (isPlaying.value) {
      player.value.pauseVideo();
    } else {
      player.value.playVideo();
    }
    isPlaying.value = !isPlaying.value;
  }
};

const handleVolumeChange = () => {
  if (player.value) {
    player.value.setVolume(volume.value);
    isMuted.value = volume.value === 0;
  }
};

const toggleMute = () => {
  if (player.value) {
    if (isMuted.value) {
      player.value.unMute();
      volume.value = player.value.getVolume();
    } else {
      player.value.mute();
      volume.value = 0;
    }
    isMuted.value = !isMuted.value;
  }
};

const handleSeek = (event) => {
  if (player.value) {
    player.value.seekTo(parseFloat(event.target.value), true);
  }
};

const stopVideo = () => {
  if (player.value) {
    player.value.stopVideo();
    isPlaying.value = false;
  }
};

const playNextTrack = () => {
  emit("next-track");
};

const playPreviousTrack = () => {
  emit("previous-track");
};

const toggleVideoVisibility = () => {
  isVideoVisible.value = !isVideoVisible.value;
  if (player.value) {
    player.value.setSize("100%", isVideoVisible.value ? "100%" : "0");
  }
};

watch(() => props.currentVideo, (newVideo) => {
  if (newVideo && newVideo.id && newVideo.id.videoId) {
    loadVideo(newVideo.id.videoId);
  } else {
    stopVideo();
  }
}, { immediate: true });

watch(() => props.queue.length, (newLength) => {
  if (newLength === 0) {
    stopVideo();
  }
});

defineExpose({
  loadVideo,
  stopVideo,
  playNextTrack,
  playPreviousTrack,
});
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