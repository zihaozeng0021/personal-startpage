<!-- src/components/SearchBar.vue -->
<template>
  <div
      class="search-bar-container"
      :style="containerStyle"
      @click.self="closeEngineMenu"
  >
    <div
        class="logo-wrapper"
        :style="logoWrapperStyle"
        @click.stop="toggleEngineMenu"
        title="Select search engine"
        tabindex="0"
        @keydown.escape="closeEngineMenu"
    >
      <img
          class="search-engine-logo"
          :style="logoStyle"
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
        :style="inputStyle"
        type="text"
        :placeholder="`Search ${currentEngine.name}`"
    />

    <button
        class="search-button"
        :style="buttonStyle"
        @click="performSearch"
        title="Search"
    >
      <img
          class="search-icon"
          :style="iconStyle"
          src="/icon/search-icon.jpg"
          alt="Search"
      />
    </button>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue'

// ─── CONSTANTS ────────────────────────────────────────────────────────────────
const BAR_WIDTH = 800
const BAR_HEIGHT = 72
const BORDER_RADIUS = 36
const BACKDROP_BLUR = '12px'

const LOGO_SIZE = 48
const LOGO_BG_OPACITY = 0.8

const INPUT_HEIGHT = 48
const INPUT_PADDING = 16
const INPUT_BORDER_RADIUS = 24

const BUTTON_SIZE = 52
const BUTTON_BG_OPACITY = 0.95

// Breakpoints
const MOBILE_BREAKPOINT = 700
const MOBILE_BAR_WIDTH = '90%'
const MOBILE_BAR_HEIGHT = 64
const MOBILE_LOGO_SIZE = 40
const MOBILE_LOGO_MARGIN = 8
const MOBILE_INPUT_HEIGHT = 40
const MOBILE_INPUT_PADDING = 12
const MOBILE_INPUT_BORDER_RADIUS = 20
const MOBILE_BUTTON_SIZE = 48

// ─── SEARCH ENGINES SETUP ──────────────────────────────────────────────────────
const engines = [
  {
    name: 'Google',
    logo: 'icon/google-logo.png',
    baseUrl: 'https://www.google.com/search?q=',
  },
  {
    name: 'GitHub',
    logo: 'icon/github-logo.png',
    baseUrl: 'https://github.com/search?q=',
  },
  {
    name: 'Google Scholar',
    logo: 'icon/google-scholar-logo.png',
    baseUrl: 'https://scholar.google.com/scholar?q=',
  },
  {
    name: 'Baidu',
    logo: 'icon/baidu-logo.png',
    baseUrl: 'https://www.baidu.com/s?wd=',
  },
  {
    name: 'YouTube',
    logo: 'icon/youtube-logo.png',
    baseUrl: 'https://www.youtube.com/results?search_query=',
  },
  {
    name: 'Bilibili',
    logo: 'icon/bilibili-logo.png',
    baseUrl: 'https://search.bilibili.com/all?keyword=',
  },
  {
    name: 'Bing',
    logo: 'icon/bing-logo.webp',
    baseUrl: 'https://www.bing.com/search?q=',
  },
]

const currentEngineIndex = ref(0)
const currentEngine = computed(() => engines[currentEngineIndex.value])

// ─── STATE ────────────────────────────────────────────────────────────────────
const query = ref('')
const showEngineMenu = ref(false)
const popover = ref(null)

const isMobile = ref(window.innerWidth <= MOBILE_BREAKPOINT)

function updateIsMobile() {
  isMobile.value = window.innerWidth <= MOBILE_BREAKPOINT
}

onMounted(() => {
  window.addEventListener('resize', updateIsMobile)
  window.addEventListener('click', handleClickOutside)
})
onBeforeUnmount(() => {
  window.removeEventListener('resize', updateIsMobile)
  window.removeEventListener('click', handleClickOutside)
})

const containerStyle = computed(() => ({
  position: 'absolute',
  left: '50%',
  top: '40%',
  transform: 'translate(-50%, -50%)',
  display: 'flex',
  alignItems: 'center',
  width: isMobile.value ? MOBILE_BAR_WIDTH : `${BAR_WIDTH}px`,
  height: isMobile.value ? `${MOBILE_BAR_HEIGHT}px` : `${BAR_HEIGHT}px`,
  padding: '0 16px',
  backgroundColor: 'rgba(255, 255, 255, 0.15)',
  backdropFilter: `blur(${BACKDROP_BLUR})`,
  WebkitBackdropFilter: `blur(${BACKDROP_BLUR})`,
  border: '1px solid rgba(255, 255, 255, 0.3)',
  borderRadius: `${BORDER_RADIUS}px`,
  boxShadow: '0 8px 24px rgba(0, 0, 0, 0.12)',
  backdropClip: 'padding-box',
}))

const logoWrapperStyle = computed(() => {
  const size = isMobile.value ? MOBILE_LOGO_SIZE : LOGO_SIZE
  const marginRight = isMobile.value ? MOBILE_LOGO_MARGIN : 12
  return {
    position: 'relative',
    flex: 'none',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'center',
    width: `${size}px`,
    height: `${size}px`,
    marginRight: `${marginRight}px`,
    backgroundColor: `rgba(255, 255, 255, ${LOGO_BG_OPACITY})`,
    borderRadius: '50%',
    boxShadow: '0 2px 8px rgba(0, 0, 0, 0.1)',
    cursor: 'pointer',
  }
})

const logoStyle = computed(() => {
  const logoDim = isMobile.value ? 20 : 24
  return {
    width: `${logoDim}px`,
    height: `${logoDim}px`,
    objectFit: 'contain',
  }
})

const inputStyle = computed(() => {
  const height = isMobile.value ? MOBILE_INPUT_HEIGHT : INPUT_HEIGHT
  const padding = isMobile.value ? MOBILE_INPUT_PADDING : INPUT_PADDING
  const borderRadius = isMobile.value
      ? MOBILE_INPUT_BORDER_RADIUS
      : INPUT_BORDER_RADIUS
  const fontSize = isMobile.value ? '0.9rem' : '1rem'

  return {
    flex: '1',
    height: `${height}px`,
    padding: `0 ${padding}px`,
    fontSize,
    fontFamily: "'PingFang SC', 'Microsoft YaHei', sans-serif",
    color: 'rgba(255, 255, 255, 0.95)',
    backgroundColor: 'rgba(255, 255, 255, 0.2)',
    border: '1px solid rgba(255, 255, 255, 0.4)',
    borderRadius: `${borderRadius}px`,
    outline: 'none',
    transition: 'background-color 0.2s, border-color 0.2s',
  }
})

const buttonStyle = computed(() => {
  const size = isMobile.value ? MOBILE_BUTTON_SIZE : BUTTON_SIZE
  const marginLeft = isMobile.value ? 8 : 12
  return {
    flex: 'none',
    display: 'flex',
    alignItems: 'center',
    justifyContent: 'center',
    width: `${size}px`,
    height: `${size}px`,
    marginLeft: `${marginLeft}px`,
    backgroundColor: `rgba(255, 255, 255, ${BUTTON_BG_OPACITY})`,
    border: 'none',
    borderRadius: '50%',
    cursor: 'pointer',
    transition: 'background-color 0.2s, transform 0.1s',
  }
})

const iconStyle = computed(() => {
  const iconDim = isMobile.value ? 20 : 24
  return {
    width: `${iconDim}px`,
    height: `${iconDim}px`,
    objectFit: 'contain',
  }
})

// ─── HANDLERS ─────────────────────────────────────────────────────────────────
function handleClickOutside(event) {
  if (
      showEngineMenu.value &&
      popover.value &&
      !popover.value.contains(event.target) &&
      !event.target.closest('.logo-wrapper')
  ) {
    showEngineMenu.value = false
  }
}

function toggleEngineMenu() {
  showEngineMenu.value = !showEngineMenu.value
}

function closeEngineMenu() {
  showEngineMenu.value = false
}

function selectEngine(idx) {
  currentEngineIndex.value = idx
  showEngineMenu.value = false
}

function performSearch() {
  const trimmed = query.value.trim()
  if (!trimmed) return
  const url = `${currentEngine.value.baseUrl}${encodeURIComponent(trimmed)}`
  window.open(url, '_blank')
}
</script>

<style scoped>
/* ─── TRANSITION FOR THE POP‐OVER ───────────────────────────────────────────── */
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

/* ─── POP‐OVER STYLING ───────────────────────────────────────────────────────── */
.engine-popover {
  position: absolute;
  top: 100%;
  left: 50%;
  transform: translateX(-50%);
  margin-top: 8px;
  padding: 6px 8px;
  background-color: rgba(255, 255, 255, 0.95);
  border-radius: 8px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.1);
  white-space: nowrap;
  display: flex;
  gap: 8px;
  z-index: 10;
}

/* ─── ENGINE ICONS ─────────────────────────────────────────────────────────── */
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

/* ─── INPUT FOCUS & PLACEHOLDER ─────────────────────────────────────────────── */
.search-input::placeholder {
  color: rgba(255, 255, 255, 0.7);
  font-weight: 500;
}
.search-input:focus {
  background-color: rgba(255, 255, 255, 0.3);
  border-color: rgba(255, 255, 255, 0.6);
}

/* ─── BUTTON EFFECTS ────────────────────────────────────────────────────────── */
.search-button:hover {
  background-color: rgba(255, 255, 255, 1);
}
.search-button:active {
  transform: scale(0.95);
}
</style>
