<!-- src/components/SearchBar.vue -->
<template>
  <div
      class="search-bar-container"
      :style="containerStyle"
  >
    <div class="logo-wrapper" :style="logoWrapperStyle">
      <img
          class="google-logo"
          :style="logoStyle"
          src="/icon/google-logo.png"
          alt="Google logo"
      />
    </div>

    <input
        v-model="query"
        @keyup.enter="performSearch"
        class="search-input"
        :style="inputStyle"
        type="text"
        placeholder="Search Google"
    />

    <button
        class="search-button"
        :style="buttonStyle"
        @click="performSearch"
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
import { ref, computed } from 'vue';

// ─── CONSTANTS ────────────────────────────────────────────────────────────────
const BAR_WIDTH = 600;
const BAR_HEIGHT = 72;
const BORDER_RADIUS = 36;
const BACKDROP_BLUR = '12px';

const LOGO_SIZE = 48;
const LOGO_BG_OPACITY = 0.8;

const INPUT_HEIGHT = 48;
const INPUT_PADDING = 16;
const INPUT_BORDER_RADIUS = 24;

const BUTTON_SIZE = 52;
const BUTTON_BG_OPACITY = 0.95;

// Breakpoints
const MOBILE_BREAKPOINT = 700;
const MOBILE_BAR_WIDTH = '90%';
const MOBILE_BAR_HEIGHT = 64;
const MOBILE_LOGO_SIZE = 40;
const MOBILE_LOGO_MARGIN = 8;
const MOBILE_INPUT_HEIGHT = 40;
const MOBILE_INPUT_PADDING = 12;
const MOBILE_INPUT_BORDER_RADIUS = 20;
const MOBILE_BUTTON_SIZE = 48;

// ─── REACTIVE STATE ──────────────────────────────────────────────────────────
const query = ref('');

// ─── COMPUTED STYLES ──────────────────────────────────────────────────────────
const containerStyle = computed(() => {
  const isMobile = window.innerWidth <= MOBILE_BREAKPOINT;
  return {
    position: 'absolute',
    left: '50%',
    top: '40%',
    transform: 'translate(-50%, -50%)',
    display: 'flex',
    alignItems: 'center',
    width: isMobile ? MOBILE_BAR_WIDTH : `${BAR_WIDTH}px`,
    height: isMobile ? `${MOBILE_BAR_HEIGHT}px` : `${BAR_HEIGHT}px`,
    padding: `0 16px`,
    backgroundColor: 'rgba(255, 255, 255, 0.15)',
    backdropFilter: `blur(${BACKDROP_BLUR})`,
    WebkitBackdropFilter: `blur(${BACKDROP_BLUR})`,
    border: '1px solid rgba(255, 255, 255, 0.3)',
    borderRadius: `${BORDER_RADIUS}px`,
    boxShadow: '0 8px 24px rgba(0, 0, 0, 0.12)',
    backdropClip: 'padding-box',
  };
});

const logoWrapperStyle = computed(() => {
  const isMobile = window.innerWidth <= MOBILE_BREAKPOINT;
  const size = isMobile ? MOBILE_LOGO_SIZE : LOGO_SIZE;
  const marginRight = isMobile ? MOBILE_LOGO_MARGIN : 12;
  return {
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
  };
});

const logoStyle = computed(() => {
  const isMobile = window.innerWidth <= MOBILE_BREAKPOINT;
  const logoDim = isMobile ? 20 : 24;
  return {
    width: `${logoDim}px`,
    height: `${logoDim}px`,
    objectFit: 'contain',
  };
});

const inputStyle = computed(() => {
  const isMobile = window.innerWidth <= MOBILE_BREAKPOINT;
  const height = isMobile ? MOBILE_INPUT_HEIGHT : INPUT_HEIGHT;
  const padding = isMobile ? MOBILE_INPUT_PADDING : INPUT_PADDING;
  const borderRadius = isMobile ? MOBILE_INPUT_BORDER_RADIUS : INPUT_BORDER_RADIUS;
  const fontSize = isMobile ? '0.9rem' : '1rem';
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
  };
});

const buttonStyle = computed(() => {
  const isMobile = window.innerWidth <= MOBILE_BREAKPOINT;
  const size = isMobile ? MOBILE_BUTTON_SIZE : BUTTON_SIZE;
  const marginLeft = isMobile ? 8 : 12;
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
  };
});

const iconStyle = computed(() => {
  const isMobile = window.innerWidth <= MOBILE_BREAKPOINT;
  const iconDim = isMobile ? 20 : 24;
  return {
    width: `${iconDim}px`,
    height: `${iconDim}px`,
    objectFit: 'contain',
  };
});

// ─── FUNCTIONS ────────────────────────────────────────────────────────────────
function performSearch() {
  const trimmed = query.value.trim();
  if (!trimmed) return;
  const url = `https://www.google.com/search?q=${encodeURIComponent(trimmed)}`;
  window.open(url, '_blank');
}
</script>

<style scoped>
.search-input::placeholder {
  color: rgba(255, 255, 255, 0.7);
  font-weight: 500;
}

.search-input:focus {
  background-color: rgba(255, 255, 255, 0.3);
  border-color: rgba(255, 255, 255, 0.6);
}

.search-button:hover {
  background-color: rgba(255, 255, 255, 1);
}

.search-button:active {
  transform: scale(0.95);
}
</style>
