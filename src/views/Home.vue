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
  <div class="min-h-screen relative overflow-hidden font-sans bg-gradient-to-b from-[#A8D8EA] via-[#B8E2F0] to-[#E8D5C4]">
    <!-- Animated fixed logo bar -->
    <transition name="fade-slide">
      <div
        v-if="showLogoBar"
        class="fixed left-1/2 top-4 -translate-x-1/2 z-50 flex items-center px-5 py-2 rounded-2xl bg-white/80 backdrop-blur-lg shadow-lg border border-white/40 animate-fade-in"
      >
        <img
          src="/daydream-penang.png"
          alt="Daydream Penang"
          class="h-10 w-auto rounded-xl"
        />
        <span class="ml-3 font-bold text-lg tracking-tight text-[#4A7C8C]">Daydream Penang</span>
      </div>
    </transition>

    <!-- Animated background shapes -->
    <div class="pointer-events-none select-none">
      <div class="absolute top-[-80px] left-[-80px] w-72 h-72 bg-pink-300/30 rounded-full blur-3xl animate-pulse-slow"></div>
      <div class="absolute bottom-[-100px] right-[-100px] w-96 h-96 bg-blue-200/40 rounded-full blur-3xl animate-pulse-slower"></div>
      <div class="absolute top-1/2 left-[-60px] w-40 h-40 bg-yellow-200/40 rounded-full blur-2xl animate-pulse"></div>
    </div>

    <!-- Parallax buildings -->
    <div class="fixed inset-0 -z-9 pointer-events-none transition-transform duration-300">
      <img
        src="/buildings-back.png"
        alt=""
        class="absolute inset-0 w-full h-full object-cover opacity-40"
        loading="eager"
      />
    </div>
    <div class="fixed inset-0 -z-8 pointer-events-none transition-transform duration-300">
      <img
        src="/buildings-front.png"
        alt=""
        class="absolute inset-0 w-full h-full object-cover opacity-60"
        loading="eager"
      />
    </div>

    <!-- Hero Section -->
    <header class="relative pt-20 sm:pt-28 lg:pt-36 pb-8 sm:pb-12">
      <div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8">
        <div
          class="backdrop-blur-md bg-white/80 rounded-3xl shadow-2xl border border-white/40 p-6 sm:p-10 lg:p-16 animate-fade-in-up"
        >
          <div class="flex flex-col items-center text-center space-y-6">
            <div class="flex items-center gap-2 text-sm font-medium text-gray-700">
              <img
                src="/hackclub.png"
                alt="Hack Club"
                class="h-6 w-6 opacity-80"
              />
              <span class="px-3 py-1 bg-red-500/80 text-white rounded-full text-xs font-bold tracking-wide">
                Hack Club Presents
              </span>
            </div>
            <img
              src="/daydream.svg"
              alt="Daydream Penang"
              class="h-20 sm:h-28 md:h-32 w-auto drop-shadow-lg animate-fade-in-up"
            />
            <div class="space-y-3">
              <p class="text-lg sm:text-xl text-[#5B8FA3] font-semibold italic animate-fade-in">
                Memories of September 27th 2025
              </p>
              <h2 class="text-2xl sm:text-3xl lg:text-4xl font-bold text-[#4A7C8C] italic animate-fade-in-up-delayed">
                Game jam for high schoolers
              </h2>
              <p class="text-sm sm:text-base text-gray-500 animate-fade-in-up-delayed" ref="headerEndRef">
                Organized by Teenagers in Penang
              </p>
            </div>
          </div>
        </div>
      </div>
    </header>

    <!-- About Section -->
    <section class="relative py-8 sm:py-12 lg:py-16">
      <div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8">
        <div
          class="backdrop-blur-md bg-white/70 rounded-3xl shadow-lg border border-white/30 p-6 sm:p-10 lg:p-12 animate-fade-in"
        >
          <div class="text-center space-y-6">
            <h2 class="text-2xl sm:text-3xl lg:text-4xl font-bold text-[#4A7C8C]">
              What is Daydream?
            </h2>
            <div class="space-y-4 text-gray-700 text-base sm:text-lg leading-relaxed">
              <p>
                Daydream Penang was a student-led hackathon and game-making
                workshop, part of Hack Club's global Daydream initiative. Our
                goal was to give high school students in Penang the opportunity to
                explore game development, even if they had never coded before.
              </p>
              <p>
                In just one day, participants built their very first playable
                games using Godot. It wasn't just about programming, it was
                about creativity, and turning ideas into something real.
              </p>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Event Gallery -->
    <section class="relative py-8 sm:py-12 lg:py-16">
      <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8">
        <h2 class="text-2xl sm:text-3xl lg:text-4xl font-bold text-[#4A7C8C] drop-shadow-lg mb-8 sm:mb-12 text-center animate-fade-in">
          Event Gallery
        </h2>
        <div v-if="loading" class="flex justify-center items-center py-20">
          <div class="backdrop-blur-md bg-white/70 rounded-2xl p-8">
            <div class="animate-spin rounded-full h-12 w-12 border-b-2 border-[#4A7C8C]"></div>
          </div>
        </div>
        <div v-else-if="error" class="text-center py-20">
          <div class="backdrop-blur-md bg-white/70 rounded-2xl p-8 inline-block">
            <p class="text-gray-700 mb-4">Failed to load media</p>
            <button
              @click="fetchMedia"
              class="px-6 py-3 bg-[#4A7C8C] text-white rounded-xl hover:bg-[#3A6C7C] transition-colors shadow-lg"
            >
              Retry
            </button>
          </div>
        </div>
        <div
          v-else
          class="grid grid-cols-1 xs:grid-cols-2 md:grid-cols-3 xl:grid-cols-4 gap-6"
        >
          <div
            v-for="(item, index) in mediaItems"
            :key="index"
            class="relative group cursor-pointer flex items-center justify-center animate-fade-in"
            @click="openLightbox(item, index)"
          >
            <div
              class="w-full aspect-[4/3] max-w-full rounded-3xl backdrop-blur-xl bg-white/30 shadow-2xl border border-white/40 flex items-center justify-center overflow-hidden transition-all duration-300 hover:scale-105 hover:shadow-3xl"
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
                    class="w-12 h-12 sm:w-16 sm:h-16 rounded-full bg-white/80 backdrop-blur-md flex items-center justify-center shadow-lg"
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
                  class="object-contain w-full h-full relative z-10 rounded-2xl"
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

    <!-- Sponsors Section -->
    <section class="relative py-8 sm:py-10 lg:py-12">
      <div class="max-w-5xl mx-auto px-4 sm:px-6 lg:px-8">
        <div
          class="backdrop-blur-md bg-white/80 rounded-2xl shadow-md border border-white/30 p-4 sm:p-6 animate-fade-in-up"
        >
          <h2
            class="text-xl sm:text-2xl font-bold text-[#4A7C8C] mb-6 text-center"
          >
            Proudly Supported By
          </h2>
          <div
            class="grid grid-cols-2 sm:grid-cols-4 gap-4 items-center justify-items-center"
          >
            <div
              v-for="sponsor in sponsors"
              :key="sponsor.name"
              class="flex items-center justify-center w-full p-2 rounded-xl hover:bg-white/40 transition-all duration-200"
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
                  :class="`${sponsor.size} w-auto object-contain opacity-80 group-hover:opacity-100 transition-all duration-400 ease-in-out drop-shadow group-hover:scale-105`"
                />
              </a>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Footer -->
    <footer class="relative py-8 sm:py-12 backdrop-blur-sm bg-white/30">
      <div
        class="text-center max-w-7xl mx-auto px-4 text-gray-500 sm:px-6 lg:px-8"
      >
        <p>
          Made with <span class="text-red-500">❤️</span> by
          <a
            href="https://github.com/leecheeyong"
            target="_blank"
            class="text-gray-800 hover:underline"
          >
            Chee Yong Lee </a
          >, Xin Ru Lim, Rui Zhe See & Khy Hern Ng
        </p>
        <p>
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
        <p class="mt-1">
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

    <!-- Lightbox Modal -->
    <Teleport to="body">
      <transition name="fade-slide">
        <div
          v-if="selectedMedia"
          class="fixed inset-0 z-50 bg-black/95 backdrop-blur-xl flex items-center justify-center p-4"
          @click="closeLightbox"
        >
          <button
            @click="closeLightbox"
            class="absolute top-4 right-4 text-white/80 hover:text-white transition-colors z-10 p-2 rounded-full hover:bg-white/10"
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
            class="absolute left-4 text-white/80 hover:text-white transition-colors z-10 p-2 rounded-full hover:bg-white/10"
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
            class="absolute right-4 text-white/80 hover:text-white transition-colors z-10 p-2 rounded-full hover:bg-white/10"
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
            <video
              v-if="isVideo(selectedMedia.url)"
              :src="selectedMedia.url"
              class="max-w-full max-h-full object-contain rounded-lg shadow-2xl"
              controls
              autoplay
            />
            <img
              v-else
              :src="selectedMedia.url"
              :alt="`Gallery image ${selectedIndex + 1}`"
              class="max-w-full max-h-full object-contain rounded-lg shadow-2xl"
            />
          </div>
        </div>
      </transition>
    </Teleport>
  </div>
</template>

<style>
/* ...existing code... */
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

@media (max-width: 640px) {
  .sm\:h-35 {
    height: 2.5rem !important;
  }
  .sm\:w-60 {
    width: 8rem !important;
  }
}

/* Animations */
@keyframes fade-in-up {
  from { opacity: 0; transform: translateY(30px);}
  to { opacity: 1; transform: translateY(0);}
}
@keyframes fade-in {
  from { opacity: 0;}
  to { opacity: 1;}
}
@keyframes fade-in-up-delayed {
  from { opacity: 0; transform: translateY(30px);}
  to { opacity: 1; transform: translateY(0);}
}
@keyframes gradient-move {
  0%, 100% { background-position: 0% 50%;}
  50% { background-position: 100% 50%;}
}
@keyframes pulse-slow {
  0%, 100% { opacity: 0.7; transform: scale(1);}
  50% { opacity: 1; transform: scale(1.08);}
}
@keyframes pulse-slower {
  0%, 100% { opacity: 0.5; transform: scale(1);}
  50% { opacity: 0.8; transform: scale(1.12);}
}
.animate-fade-in-up {
  animation: fade-in-up 0.8s cubic-bezier(.4,0,.2,1) both;
}
.animate-fade-in {
  animation: fade-in 1s cubic-bezier(.4,0,.2,1) both;
}
.animate-fade-in-up-delayed {
  animation: fade-in-up-delayed 1.2s cubic-bezier(.4,0,.2,1) both;
}
.animate-gradient {
  background-size: 200% 200%;
  animation: gradient-move 4s ease-in-out infinite;
}
.animate-pulse-slow {
  animation: pulse-slow 5s ease-in-out infinite;
}
.animate-pulse-slower {
  animation: pulse-slower 8s ease-in-out infinite;
}
</style>
