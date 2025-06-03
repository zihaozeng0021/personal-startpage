<!-- src/components/SearchBar.vue -->
<template>
  <div class="search-bar-container" @click.self="closeEngineMenu">
    <div
        class="logo-wrapper"
        @click.stop="toggleEngineMenu"
        title="Select search engine"
        tabindex="0"
        @keydown.escape="closeEngineMenu"
    >
      <img
          class="search-engine-logo"
          :src="currentEngine.logo"
          :alt="currentEngine.name + ' logo'"
      />

      <transition name="engine-popover">
        <div
            v-if="showEngineMenu"
            class="engine-popover"
            ref="popover"
            @click.stop
        >
          <div
              v-for="(engine, idx) in engines"
              :key="engine.name"
              class="engine-icon-item"
              :class="{ selected: idx === currentEngineIndex }"
              @click="selectEngine(idx)"
              :title="engine.name"
          >
            <img
                class="engine-icon"
                :src="engine.logo"
                :alt="engine.name + ' logo'"
            />
          </div>
        </div>
      </transition>
    </div>

    <input
        v-model="query"
        @keyup.enter="performSearch"
        class="search-input"
        type="text"
        :placeholder="`Search ${currentEngine.name}`"
    />

    <button
        class="search-button"
        @click="performSearch"
        title="Search"
    >
      <img
          class="search-icon"
          src="/icon/search-icon.jpg"
          alt="Search"
      />
    </button>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from "vue";

// ─── SEARCH ENGINES SETUP ──────────────────────────────────────────────────────
const engines = [
  {
    name: "Google",
    logo: "icon/google-logo.png",
    baseUrl: "https://www.google.com/search?q=",
  },
  {
    name: "GitHub",
    logo: "icon/github-logo.png",
    baseUrl: "https://github.com/search?q=",
  },
  {
    name: "Google Scholar",
    logo: "icon/google-scholar-logo.png",
    baseUrl: "https://scholar.google.com/scholar?q=",
  },
  {
    name: "Baidu",
    logo: "icon/baidu-logo.png",
    baseUrl: "https://www.baidu.com/s?wd=",
  },
  {
    name: "YouTube",
    logo: "icon/youtube-logo.png",
    baseUrl: "https://www.youtube.com/results?search_query=",
  },
  {
    name: "Bilibili",
    logo: "icon/bilibili-logo.png",
    baseUrl: "https://search.bilibili.com/all?keyword=",
  },
  {
    name: "Bing",
    logo: "icon/bing-logo.webp",
    baseUrl: "https://www.bing.com/search?q=",
  },
];

const currentEngineIndex = ref(0);
const currentEngine = computed(() => engines[currentEngineIndex.value]);

// ─── STATE ────────────────────────────────────────────────────────────────────
const query = ref("");
const showEngineMenu = ref(false);
const popover = ref(null);

// ─── LIFECYCLE: detect clicks outside popover ─────────────────────────────────
function handleClickOutside(event) {
  if (
      showEngineMenu.value &&
      popover.value &&
      !popover.value.contains(event.target) &&
      !event.target.closest(".logo-wrapper")
  ) {
    showEngineMenu.value = false;
  }
}

onMounted(() => {
  window.addEventListener("click", handleClickOutside);
});
onBeforeUnmount(() => {
  window.removeEventListener("click", handleClickOutside);
});

// ─── METHODS ─────────────────────────────────────────────────────────────────
function toggleEngineMenu() {
  showEngineMenu.value = !showEngineMenu.value;
}

function closeEngineMenu() {
  showEngineMenu.value = false;
}

function selectEngine(idx) {
  currentEngineIndex.value = idx;
  showEngineMenu.value = false;
}

function performSearch() {
  const trimmed = query.value.trim();
  if (!trimmed) return;
  const url = `${currentEngine.value.baseUrl}${encodeURIComponent(trimmed)}`;
  window.open(url, "_blank");
}
</script>

<style scoped>
/* ──────────────────────────────────────────────────────────────────────────── */
/* Mobile‐first: viewport width ≤700px */
.search-bar-container {
  position: absolute;
  left: 50%;
  top: 40%;
  transform: translate(-50%, -50%);
  display: flex;
  align-items: center;
  width: 90%;
  max-width: none; /* will be overridden in larger breakpoints */
  height: 64px;
  padding: 0 12px;
  background-color: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(12px);
  -webkit-backdrop-filter: blur(12px);
  border: 1px solid rgba(255, 255, 255, 0.3);
  border-radius: 32px;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.12);
  backdrop-clip: padding-box;
  z-index: 5;
}

.logo-wrapper {
  position: relative;
  flex: none;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 40px;
  height: 40px;
  margin-right: 8px;
  background-color: rgba(255, 255, 255, 0.8);
  border-radius: 50%;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  cursor: pointer;
}

.search-engine-logo {
  width: 20px;
  height: 20px;
  object-fit: contain;
}

.search-input {
  flex: 1;
  height: 40px;
  padding: 0 12px;
  font-size: 0.9rem;
  font-family: "PingFang SC", "Microsoft YaHei", sans-serif;
  color: rgba(255, 255, 255, 0.95);
  background-color: rgba(255, 255, 255, 0.2);
  border: 1px solid rgba(255, 255, 255, 0.4);
  border-radius: 20px;
  outline: none;
  transition: background-color 0.2s, border-color 0.2s;
}

.search-input::placeholder {
  color: rgba(255, 255, 255, 0.7);
  font-weight: 500;
}

.search-input:focus {
  background-color: rgba(255, 255, 255, 0.3);
  border-color: rgba(255, 255, 255, 0.6);
}

.search-button {
  flex: none;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 48px;
  height: 48px;
  margin-left: 8px;
  background-color: rgba(255, 255, 255, 0.95);
  border: none;
  border-radius: 50%;
  cursor: pointer;
  transition: background-color 0.2s, transform 0.1s;
}

.search-button:hover {
  background-color: rgba(255, 255, 255, 1);
}

.search-button:active {
  transform: scale(0.95);
}

.search-icon {
  width: 20px;
  height: 20px;
  object-fit: contain;
}

/* ─── Engine‐popover Animations ─────────────────────────────────────────────── */
.engine-popover-enter-active,
.engine-popover-leave-active {
  transition: all 0.15s ease-out;
}
.engine-popover-enter-from,
.engine-popover-leave-to {
  opacity: 0;
  transform: scale(0.8);
}
.engine-popover-enter-to,
.engine-popover-leave-from {
  opacity: 1;
  transform: scale(1);
}

/* ─── Engine‐popover Panel ──────────────────────────────────────────────────── */
.engine-popover {
  position: absolute;
  top: 100%;
  left: 50%;
  transform: translateX(-50%);
  margin-top: 6px;
  padding: 6px 8px;
  background-color: rgba(255, 255, 255, 0.95);
  border-radius: 8px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
  white-space: nowrap;
  display: flex;
  gap: 8px;
  z-index: 10;
}

.engine-icon-item {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  background-color: rgba(245, 245, 245, 1);
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: background-color 0.2s, transform 0.1s;
}

.engine-icon-item:hover {
  background-color: rgba(230, 230, 230, 1);
  transform: scale(1.05);
}

.engine-icon-item.selected {
  box-shadow: 0 0 0 2px #3b82f6;
}

.engine-icon {
  max-width: 24px;
  max-height: 24px;
  object-fit: contain;
}

/* ─── Responsive Breakpoint: >700px ─────────────────────────────────────────── */
@media (min-width: 701px) {
  .search-bar-container {
    width: 800px;
    max-width: 800px;
    height: 72px;
    padding: 0 16px;
    border-radius: 36px;
  }

  .logo-wrapper {
    width: 48px;
    height: 48px;
    margin-right: 12px;
  }
  .search-engine-logo {
    width: 24px;
    height: 24px;
  }

  .search-input {
    height: 48px;
    padding: 0 16px;
    font-size: 1rem;
    border-radius: 24px;
  }

  .search-button {
    width: 52px;
    height: 52px;
    margin-left: 12px;
  }
  .search-icon {
    width: 24px;
    height: 24px;
  }
}
</style>
