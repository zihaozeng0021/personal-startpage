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

// Base (mid-sized) dimensions
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

// Mobile dimensions (<= 900px)
const MOBILE_BREAKPOINT = 900
const MOBILE_BAR_WIDTH = '90%'
const MOBILE_BAR_HEIGHT = 64
const MOBILE_LOGO_SIZE = 40
const MOBILE_LOGO_MARGIN = 8
const MOBILE_INPUT_HEIGHT = 40
const MOBILE_INPUT_PADDING = 12
const MOBILE_INPUT_BORDER_RADIUS = 20
const MOBILE_BUTTON_SIZE = 48

// Large-screen dimensions (>= 2560px)
const LARGE_BREAKPOINT = 2560
const LARGE_BAR_WIDTH = 1500
const LARGE_BAR_HEIGHT = 120
const LARGE_BORDER_RADIUS = 60

const LARGE_LOGO_SIZE = 84
const LARGE_LOGO_MARGIN = 24

const LARGE_INPUT_HEIGHT = 84
const LARGE_INPUT_PADDING = 30
const LARGE_INPUT_BORDER_RADIUS = 42

const LARGE_BUTTON_SIZE = 80
const LARGE_ICON_DIM = 36

// ─── SEARCH ENGINES SETUP ──────────────────────────────────────────────────────
const engines = [
  { name: 'Google',          logo: 'icon/google-logo.png',          baseUrl: 'https://www.google.com/search?q=' },
  { name: 'GitHub',          logo: 'icon/github-logo.png',          baseUrl: 'https://github.com/search?q=' },
  { name: 'Google Scholar',  logo: 'icon/google-scholar-logo.png',  baseUrl: 'https://scholar.google.com/scholar?q=' },
  { name: 'Baidu',           logo: 'icon/baidu-logo.png',           baseUrl: 'https://www.baidu.com/s?wd=' },
  { name: 'YouTube',         logo: 'icon/youtube-logo.png',         baseUrl: 'https://www.youtube.com/results?search_query=' },
  { name: 'Bilibili',        logo: 'icon/bilibili-logo.png',        baseUrl: 'https://search.bilibili.com/all?keyword=' },
  { name: 'Bing',            logo: 'icon/bing-logo.webp',           baseUrl: 'https://www.bing.com/search?q=' },
]

const currentEngineIndex = ref(0)
const currentEngine = computed(() => engines[currentEngineIndex.value])

// ─── STATE ────────────────────────────────────────────────────────────────────
const query = ref('')
const showEngineMenu = ref(false)
const popover = ref(null)

const isMobile = ref(window.innerWidth <= MOBILE_BREAKPOINT)
const isLarge  = ref(window.innerWidth >= LARGE_BREAKPOINT)

function updateSizeFlags() {
  isMobile.value = window.innerWidth <= MOBILE_BREAKPOINT
  isLarge.value  = window.innerWidth >= LARGE_BREAKPOINT
}

onMounted(() => {
  window.addEventListener('resize', updateSizeFlags)
  window.addEventListener('click', handleClickOutside)
})
onBeforeUnmount(() => {
  window.removeEventListener('resize', updateSizeFlags)
  window.removeEventListener('click', handleClickOutside)
})

// ─── COMPUTED STYLES ──────────────────────────────────────────────────────────

const containerStyle = computed(() => {
  let width, height, borderRadius
  if (isMobile.value) {
    width = MOBILE_BAR_WIDTH
    height = `${MOBILE_BAR_HEIGHT}px`
    borderRadius = `${MOBILE_INPUT_BORDER_RADIUS * 2.3}px`
  } else if (isLarge.value) {
    width = `${LARGE_BAR_WIDTH}px`
    height = `${LARGE_BAR_HEIGHT}px`
    borderRadius = `${LARGE_BORDER_RADIUS}px`
  } else {
    width = `${BAR_WIDTH}px`
    height = `${BAR_HEIGHT}px`
    borderRadius = `${BORDER_RADIUS}px`
  }

  return {
    position: 'absolute',
    left: '50%',
    top: '40%',
    transform: 'translate(-50%, -50%)',
    display: 'flex',
    alignItems: 'center',
    width,
    height,
    padding: '0 16px',
    backgroundColor: 'rgba(255, 255, 255, 0.15)',
    backdropFilter: `blur(${BACKDROP_BLUR})`,
    WebkitBackdropFilter: `blur(${BACKDROP_BLUR})`,
    border: '1px solid rgba(255, 255, 255, 0.3)',
    borderRadius,
    boxShadow: '0 8px 24px rgba(0, 0, 0, 0.12)',
    backdropClip: 'padding-box',
    overflow: 'visible',
  }
})

const logoWrapperStyle = computed(() => {
  let size, marginRight
  if (isMobile.value) {
    size = MOBILE_LOGO_SIZE
    marginRight = MOBILE_LOGO_MARGIN
  } else if (isLarge.value) {
    size = LARGE_LOGO_SIZE
    marginRight = LARGE_LOGO_MARGIN
  } else {
    size = LOGO_SIZE
    marginRight = 12
  }

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
  let logoDim
  if (isMobile.value) {
    logoDim = 20
  } else if (isLarge.value) {
    logoDim = 28
  } else {
    logoDim = 24
  }

  return {
    width: `${logoDim}px`,
    height: `${logoDim}px`,
    objectFit: 'contain',
  }
})

const inputStyle = computed(() => {
  let height, padding, borderRadius, fontSize
  if (isMobile.value) {
    height = MOBILE_INPUT_HEIGHT
    padding = MOBILE_INPUT_PADDING
    borderRadius = MOBILE_INPUT_BORDER_RADIUS
    fontSize = '0.9rem'
  } else if (isLarge.value) {
    height = LARGE_INPUT_HEIGHT
    padding = LARGE_INPUT_PADDING
    borderRadius = LARGE_INPUT_BORDER_RADIUS
    fontSize = '1.1rem'
  } else {
    height = INPUT_HEIGHT
    padding = INPUT_PADDING
    borderRadius = INPUT_BORDER_RADIUS
    fontSize = '1rem'
  }

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
  let size, marginLeft
  if (isMobile.value) {
    size = MOBILE_BUTTON_SIZE
    marginLeft = 8
  } else if (isLarge.value) {
    size = LARGE_BUTTON_SIZE
    marginLeft = 16
  } else {
    size = BUTTON_SIZE
    marginLeft = 12
  }

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
  let iconDim
  if (isMobile.value) {
    iconDim = 20
  } else if (isLarge.value) {
    iconDim = LARGE_ICON_DIM
  } else {
    iconDim = 24
  }

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
.search-bar-container {
  overflow: visible;
}

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

@media (max-width: 700px) {
  .engine-popover {
    left: 0;
    transform: translateX(0);
  }
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

@media (min-width: 2560px) {
  .engine-icon-item {
    width: 48px;
    height: 48px;
  }
  .engine-icon {
    max-width: 32px;
    max-height: 32px;
  }
}
</style>
