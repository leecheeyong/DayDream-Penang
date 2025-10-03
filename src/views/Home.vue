<script setup>
import { ref, onMounted } from "vue";

const mediaItems = ref([]);
const loading = ref(true);
const error = ref(false);
const selectedMedia = ref(null);
const selectedIndex = ref(-1);
const showLogoBar = ref(false);
const headerEndRef = ref(null);

const isVideo = (url) => {
  const videoExtensions = [".mp4", ".webm", ".mov", ".avi"];
  return videoExtensions.some((ext) => url.toLowerCase().includes(ext));
};

const fetchMedia = async () => {
  try {
    loading.value = true;
    error.value = false;
    const response = await fetch("https://leecheeyong.vercel.app/daydream");
    if (!response.ok) throw new Error("Failed to fetch media");
    const data = await response.json();
    mediaItems.value = data.map((item) => ({
      url: item.url,
      loading: true,
      error: false,
    }));
  } catch (err) {
    error.value = true;
    console.error("[v0] Error fetching media:", err);
  } finally {
    loading.value = false;
  }
};

const openLightbox = (item, index) => {
  selectedMedia.value = item;
  selectedIndex.value = index;
};

const closeLightbox = () => {
  selectedMedia.value = null;
  selectedIndex.value = -1;
};

const nextMedia = () => {
  if (selectedIndex.value < mediaItems.value.length - 1) {
    selectedIndex.value++;
    selectedMedia.value = mediaItems.value[selectedIndex.value];
  }
};

const prevMedia = () => {
  if (selectedIndex.value > 0) {
    selectedIndex.value--;
    selectedMedia.value = mediaItems.value[selectedIndex.value];
  }
};

const handleMediaLoad = (index) => {
  if (mediaItems.value[index]) {
    mediaItems.value[index].loading = false;
  }
};

const handleMediaError = (index) => {
  if (mediaItems.value[index]) {
    mediaItems.value[index].error = true;
    mediaItems.value[index].loading = false;
  }
};

const handleScroll = () => {
  if (!headerEndRef.value) return;
  const rect = headerEndRef.value.getBoundingClientRect();
  showLogoBar.value = rect.bottom < 0;
};

onMounted(() => {
  fetchMedia();
  window.addEventListener("scroll", handleScroll, { passive: true });
  handleScroll();
  document.addEventListener("keydown", (e) => {
    if (!selectedMedia.value) return;
    if (e.key === "Escape") closeLightbox();
    if (e.key === "ArrowRight") nextMedia();
    if (e.key === "ArrowLeft") prevMedia();
  });
});

const sponsors = [
  {
    name: "Hack Club",
    logo: "./hackclub.png",
    size: "h-16 sm:h-20",
    url: "https://hackclub.com",
  },
  {
    name: "Penang Science Cluster",
    logo: "./psc.webp",
    size: "h-20 sm:h-24",
    url: "https://pscpen.com",
  },
  {
    name: "Cytron Technologies",
    logo: "./cytron.png",
    size: "h-46",
    url: "https://my.cytron.io",
  },
  {
    name: "JukeboxPrint",
    logo: "./jukebox.png",
    size: "h-32 sm:h-36",
    url: "https://jukeboxprint.com",
  },
];
</script>

<template>
  <div class="min-h-screen relative overflow-hidden">
    <transition name="fade-slide">
      <div
        v-if="showLogoBar"
        class="fixed left-1/2 -translate-y-3 -translate-x-1/2 z-50 flex items-center transition-all duration-500"
      >
        <img
          src="/daydream-penang.png"
          alt="Daydream Penang"
          class="sm:h-35 sm:w-60"
        />
      </div>
    </transition>
    <div
      class="fixed inset-0 bg-gradient-to-b from-[#A8D8EA] via-[#B8E2F0] to-[#E8D5C4] -z-10"
    ></div>
    <div class="fixed inset-0 -z-9 pointer-events-none">
      <img
        src="/buildings-back.png"
        alt=""
        class="absolute inset-0 w-full h-full object-cover opacity-40"
        loading="eager"
      />
    </div>
    <div class="fixed inset-0 -z-8 pointer-events-none">
      <img
        src="/buildings-front.png"
        alt=""
        class="absolute inset-0 w-full h-full object-cover opacity-60"
        loading="eager"
      />
    </div>
    <header class="relative pt-12 sm:pt-16 lg:pt-20 pb-8 sm:pb-12">
      <div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8">
        <div
          class="backdrop-blur-2xl bg-white/60 rounded-[2.5rem] shadow-2xl border border-white/40 p-8 sm:p-12 lg:p-16 glassy-animate"
        >
          <div class="flex flex-col items-center text-center space-y-6">
            <div
              class="flex items-center gap-2 text-sm font-medium text-gray-700"
            >
              <img
                src="/hackclub.png"
                alt="Hack Club"
                class="h-6 w-6 opacity-80"
              />
              <span
                class="px-3 py-1 bg-red-500/80 text-white rounded-full text-xs font-bold tracking-wide animate-fadein"
              >
                Hack Club Presents
              </span>
            </div>
            <img
              src="/daydream.svg"
              alt="Daydream Penang"
              class="h-20 sm:h-28 md:h-32 w-auto drop-shadow-xl animate-pop"
            />
            <div class="space-y-3">
              <p class="text-lg sm:text-xl text-[#5B8FA3] font-semibold italic animate-fadein">
                Memories of September 27th 2025
              </p>
              <h2
                class="text-2xl sm:text-3xl lg:text-4xl font-bold text-[#4A7C8C] italic animate-slidein"
              >
                Game jam for high schoolers
              </h2>
              <p class="text-sm sm:text-base text-gray-500" ref="headerEndRef">
                Organized by Teenagers in Penang
              </p>
            </div>
          </div>
        </div>
      </div>
    </header>
    <section class="relative py-8 sm:py-12 lg:py-16">
      <div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8">
        <div
          class="backdrop-blur-2xl bg-gradient-to-br from-white/70 via-white/40 to-[#e0e7ef]/60 rounded-[2.5rem] shadow-xl border border-white/40 p-6 sm:p-10 lg:p-12 glassy-animate"
        >
          <div class="text-center space-y-6">
            <h2
              class="text-2xl sm:text-3xl lg:text-4xl font-bold text-[#4A7C8C] animate-slidein"
            >
              What is Daydream?
            </h2>
            <div
              class="space-y-4 text-gray-700 text-base sm:text-lg leading-relaxed"
            >
              <p class="animate-fadein">
                Daydream Penang was a student-led hackathon and game-making
                workshop, part of Hack Club's global Daydream initiative. Our
                goal was to give high school students in Penang the opportunity to
                explore game development, even if they had never coded before.
              </p>
              <p class="animate-fadein delay-100">
                In just one day, participants built their very first playable
                games using Godot. It wasn't just about programming, it was
                about creativity, and turning ideas into something real.
              </p>
            </div>
          </div>
        </div>
      </div>
    </section>
    <section class="relative py-8 sm:py-12 lg:py-16">
      <div class="max-w-7xl mx-auto px-2 sm:px-6 lg:px-8">
        <h2
          class="text-2xl sm:text-3xl lg:text-4xl font-bold text-[#4A7C8C] drop-shadow-lg mb-8 sm:mb-12 text-center animate-slidein"
        >
          Event Gallery
        </h2>
        <div v-if="loading" class="flex justify-center items-center py-20">
          <div
            class="backdrop-blur-2xl bg-white/70 rounded-2xl p-8 shadow-xl glassy-animate"
          >
            <div
              class="animate-spin rounded-full h-12 w-12 border-b-2 border-[#4A7C8C]"
            ></div>
          </div>
        </div>
        <div v-else-if="error" class="text-center py-20">
          <div
            class="backdrop-blur-2xl bg-white/70 rounded-2xl p-8 inline-block shadow-xl glassy-animate"
          >
            <p class="text-gray-700 mb-4">Failed to load media</p>
            <button
              @click="fetchMedia"
              class="px-6 py-3 bg-[#4A7C8C] text-white rounded-xl hover:bg-[#3A6C7C] transition-all shadow-lg hover:scale-105 focus:scale-105 focus:outline-none focus:ring-2 focus:ring-[#4A7C8C] animate-pop"
            >
              Retry
            </button>
          </div>
        </div>
        <div
          v-else
          class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 gap-8 md:gap-10"
        >
          <div
            v-for="(item, index) in mediaItems"
            :key="index"
            class="relative group cursor-pointer flex items-center justify-center animate-fadein"
            @click="openLightbox(item, index)"
            tabindex="0"
            @keydown.enter="openLightbox(item, index)"
          >
            <div
              class="w-full aspect-[4/3] max-w-full rounded-[2rem] backdrop-blur-2xl bg-gradient-to-br from-white/60 via-white/30 to-[#e0e7ef]/40 shadow-2xl border border-white/40 flex items-center justify-center overflow-hidden transition-all duration-300 hover:scale-105 hover:shadow-3xl hover:bg-white/50 glassy-animate"
            >
              <div
                v-if="isVideo(item.url)"
                class="relative w-full h-full flex items-center justify-center"
              >
                <video
                  :src="item.url"
                  class="w-full h-full object-cover rounded-2xl"
                  preload="metadata"
                  @loadeddata="handleMediaLoad(index)"
                  @error="handleMediaError(index)"
                  muted
                />
                <div
                  class="absolute inset-0 flex items-center justify-center bg-gradient-to-br from-[#FF6B9D]/20 to-[#4A90E2]/20 group-hover:from-[#FF6B9D]/30 group-hover:to-[#4A90E2]/30 transition-all"
                >
                  <div
                    class="w-12 h-12 sm:w-16 sm:h-16 rounded-full bg-white/80 backdrop-blur-md flex items-center justify-center shadow-lg animate-pop"
                  >
                    <svg
                      class="w-6 h-6 sm:w-8 sm:h-8 text-[#4A7C8C] ml-1"
                      fill="currentColor"
                      viewBox="0 0 24 24"
                    >
                      <path d="M8 5v14l11-7z" />
                    </svg>
                  </div>
                </div>
              </div>
              <div
                v-else
                class="relative w-full h-full flex items-center justify-center"
              >
                <div
                  class="absolute inset-0 z-0 rounded-3xl bg-gradient-to-br from-white/60 via-white/30 to-[#e0e7ef]/40 backdrop-blur-xl"
                ></div>
                <img
                  :src="item.url"
                  :alt="`Gallery image ${index + 1}`"
                  class="object-contain w-full h-full relative z-10 rounded-2xl transition-transform duration-300 group-hover:scale-105"
                  loading="lazy"
                  @load="handleMediaLoad(index)"
                  @error="handleMediaError(index)"
                  style="background: transparent"
                />
                <div class="absolute inset-0 pointer-events-none z-5">
                  <div
                    class="w-full h-full rounded-3xl"
                    style="
                      background: inherit;
                      filter: blur(18px);
                      opacity: 0.18;
                    "
                  ></div>
                </div>
              </div>
              <div
                v-if="item.loading"
                class="absolute inset-0 flex items-center justify-center bg-white/80 backdrop-blur-xl z-20 rounded-3xl"
              >
                <div
                  class="animate-spin rounded-full h-8 w-8 border-b-2 border-[#4A7C8C]"
                ></div>
              </div>
              <div
                v-if="item.error"
                class="absolute inset-0 flex items-center justify-center bg-white/80 backdrop-blur-xl z-20 rounded-3xl"
              >
                <p class="text-gray-500 text-sm">Failed to load</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </section>
    <section class="relative py-8 sm:py-10 lg:py-12">
      <div class="max-w-5xl mx-auto px-4 sm:px-6 lg:px-8">
        <div
          class="backdrop-blur-2xl bg-gradient-to-br from-white/70 via-white/40 to-[#e0e7ef]/60 rounded-2xl shadow-xl border border-white/30 p-4 sm:p-6 glassy-animate"
        >
          <h2
            class="text-xl sm:text-2xl font-bold text-[#4A7C8C] mb-6 text-center animate-slidein"
          >
            Proudly Supported By
          </h2>
          <div
            class="grid grid-cols-2 sm:grid-cols-4 gap-4 items-center justify-items-center"
          >
            <div
              v-for="sponsor in sponsors"
              :key="sponsor.name"
              class="flex items-center justify-center w-full p-2 rounded-xl hover:bg-white/40 transition-all duration-200 glassy-animate"
            >
              <a
                :href="sponsor.url"
                target="_blank"
                rel="noopener noreferrer"
                class="flex items-center justify-center w-full group"
                :aria-label="`Visit ${sponsor.name}`"
                tabindex="0"
              >
                <img
                  :src="sponsor.logo"
                  :alt="sponsor.name"
                  :class="`${sponsor.size} w-auto object-contain opacity-80 group-hover:opacity-100 transition-all duration-400 ease-in-out drop-shadow group-hover:scale-110 animate-pop`"
                />
              </a>
            </div>
          </div>
        </div>
      </div>
    </section>
    <footer class="relative py-8 sm:py-12 backdrop-blur-2xl bg-gradient-to-t from-white/60 via-white/30 to-transparent glassy-animate">
      <div
        class="text-center max-w-7xl mx-auto px-4 text-gray-500 sm:px-6 lg:px-8"
      >
        <p class="animate-fadein">
          Made with <span class="text-red-500 animate-heartbeat">❤️</span> by
          <a
            href="https://github.com/leecheeyong"
            target="_blank"
            class="text-gray-800 hover:underline"
          >
            Chee Yong Lee </a
          >, Xin Ru Lim, Rui Zhe See & Khy Hern Ng
        </p>
        <p class="animate-fadein delay-100">
          <a
            href="https://www.instagram.com/daydreampenang"
            class="underline hover:text-gray-700"
            target="_blank"
            >Instagram</a
          >
          ·
          <a
            href="https://hackclub.com/slack/"
            class="underline hover:text-gray-700"
            target="_blank"
            >#daydream-penang</a
          >
          ·
          <a
            href="https://daydream.hackclub.com"
            target="_blank"
            class="underline hover:text-gray-700"
            >Global Event</a
          >
        </p>
        <p class="mt-1 animate-fadein delay-200">
          Open source under the terms of
          <a
            href="https://github.com/leecheeyong/daydream-penang/blob/main/LICENSE"
            target="_blank"
            class="text-gray-700 hover:underline"
            >MIT License</a
          >
        </p>
      </div>
    </footer>
    <Teleport to="body">
      <transition name="lightbox-fade">
        <div
          v-if="selectedMedia"
          class="fixed inset-0 z-50 bg-black/80 backdrop-blur-2xl flex items-center justify-center p-4 animate-fadein"
          @click="closeLightbox"
        >
          <button
            @click="closeLightbox"
            class="absolute top-4 right-4 text-white/80 hover:text-white transition-colors z-10 p-2 rounded-full hover:bg-white/10 backdrop-blur-md shadow-lg"
            aria-label="Close"
          >
            <svg
              class="w-8 h-8"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M6 18L18 6M6 6l12 12"
              />
            </svg>
          </button>
          <button
            v-if="selectedIndex > 0"
            @click.stop="prevMedia"
            class="absolute left-4 text-white/80 hover:text-white transition-colors z-10 p-2 rounded-full hover:bg-white/10 backdrop-blur-md shadow-lg"
            aria-label="Previous"
          >
            <svg
              class="w-8 h-8"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M15 19l-7-7 7-7"
              />
            </svg>
          </button>
          <button
            v-if="selectedIndex < mediaItems.length - 1"
            @click.stop="nextMedia"
            class="absolute right-4 text-white/80 hover:text-white transition-colors z-10 p-2 rounded-full hover:bg-white/10 backdrop-blur-md shadow-lg"
            aria-label="Next"
          >
            <svg
              class="w-8 h-8"
              fill="none"
              stroke="currentColor"
              viewBox="0 0 24 24"
            >
              <path
                stroke-linecap="round"
                stroke-linejoin="round"
                stroke-width="2"
                d="M9 5l7 7-7 7"
              />
            </svg>
          </button>
          <div
            class="absolute top-4 left-4 text-white/90 text-sm backdrop-blur-sm bg-black/30 px-3 py-2 rounded-full z-10"
          >
            {{ selectedIndex + 1 }} / {{ mediaItems.length }}
          </div>
          <div
            class="max-w-7xl max-h-full w-full h-full flex items-center justify-center"
            @click.stop
          >
            <transition name="media-zoom">
              <video
                v-if="isVideo(selectedMedia.url)"
                :src="selectedMedia.url"
                class="max-w-full max-h-full object-contain rounded-2xl shadow-2xl glassy-animate"
                controls
                autoplay
              />
              <img
                v-else
                :src="selectedMedia.url"
                :alt="`Gallery image ${selectedIndex + 1}`"
                class="max-w-full max-h-full object-contain rounded-2xl shadow-2xl glassy-animate"
              />
            </transition>
          </div>
        </div>
      </transition>
    </Teleport>
  </div>
</template>

<style>
.fade-slide-enter-active,
.fade-slide-leave-active {
  transition:
    opacity 0.4s cubic-bezier(0.4, 0, 0.2, 1),
    transform 0.4s cubic-bezier(0.4, 0, 0.2, 1);
}
.fade-slide-enter-from,
.fade-slide-leave-to {
  opacity: 0;
  transform: translateY(-24px) scale(0.98);
}
.fade-slide-enter-to,
.fade-slide-leave-from {
  opacity: 1;
  transform: translateY(0) scale(1);
}

.lightbox-fade-enter-active,
.lightbox-fade-leave-active {
  transition: opacity 0.35s cubic-bezier(0.4, 0, 0.2, 1);
}
.lightbox-fade-enter-from,
.lightbox-fade-leave-to {
  opacity: 0;
}
.lightbox-fade-enter-to,
.lightbox-fade-leave-from {
  opacity: 1;
}
.media-zoom-enter-active,
.media-zoom-leave-active {
  transition: transform 0.35s cubic-bezier(0.4, 0, 0.2, 1), opacity 0.35s;
}
.media-zoom-enter-from,
.media-zoom-leave-to {
  opacity: 0;
  transform: scale(0.95);
}
.media-zoom-enter-to,
.media-zoom-leave-from {
  opacity: 1;
  transform: scale(1);
}

@keyframes pop {
  0% { transform: scale(0.92); opacity: 0.7; }
  80% { transform: scale(1.04); opacity: 1; }
  100% { transform: scale(1); }
}
@keyframes fadein {
  from { opacity: 0; transform: translateY(16px);}
  to { opacity: 1; transform: none;}
}
@keyframes slidein {
  from { opacity: 0; transform: translateY(32px);}
  to { opacity: 1; transform: none;}
}
@keyframes heartbeat {
  0%, 100% { transform: scale(1);}
  10%, 30% { transform: scale(1.15);}
  20%, 40% { transform: scale(0.95);}
  50% { transform: scale(1.1);}
  60% { transform: scale(1);}
}
.glassy-animate {
  animation: fadein 0.7s cubic-bezier(0.4,0,0.2,1);
  will-change: opacity, transform;
}
.animate-pop {
  animation: pop 0.5s cubic-bezier(0.4,0,0.2,1);
}
.animate-fadein {
  animation: fadein 0.7s cubic-bezier(0.4,0,0.2,1);
}
.animate-fadein.delay-100 {
  animation-delay: 0.1s;
}
.animate-fadein.delay-200 {
  animation-delay: 0.2s;
}
.animate-slidein {
  animation: slidein 0.7s cubic-bezier(0.4,0,0.2,1);
}
.animate-heartbeat {
  animation: heartbeat 1.2s infinite;
}
</style>
