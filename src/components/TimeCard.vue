<!-- src/components/TimeCard.vue -->
<template>
  <div class="time-weather" :style="containerStyle">
    <p class="date-text" :style="dateStyle">{{ dateText }}</p>
    <p class="time-text" :style="timeStyle">{{ timeText }}</p>

    <div v-if="weatherData">
      <p class="weather-text" :style="weatherStyle">
        Weather: {{ weatherData.current_weather.weathercodeDesc }},
        Temperature: {{ weatherData.current_weather.temperature }} ℃
      </p>
    </div>

    <p
        v-else-if="errorMsg"
        class="error-text"
        :style="weatherStyle"
    >{{ errorMsg }}</p>
    <p
        v-else
        class="loading-text"
        :style="weatherStyle"
    >Retrieving weather information...</p>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount } from 'vue';

// ─── CONSTANTS ────────────────────────────────────────────────────────────────
const CARD_WIDTH = 600;
const CARD_HEIGHT = 150;
const MOBILE_BREAKPOINT = 700;

const WEATHER_CODE_MAP = {
  0: 'Clear',
  1: 'Mainly clear',
  2: 'Partly cloudy',
  3: 'Overcast',
  45: 'Fog',
  48: 'Depositing rime fog',
  51: 'Light drizzle',
  53: 'Moderate drizzle',
  55: 'Dense drizzle',
  56: 'Light freezing drizzle',
  57: 'Dense freezing drizzle',
  61: 'Slight rain',
  63: 'Moderate rain',
  65: 'Heavy rain',
  66: 'Light freezing rain',
  67: 'Heavy freezing rain',
  71: 'Slight snow fall',
  73: 'Moderate snow fall',
  75: 'Heavy snow fall',
  77: 'Snow grains',
  80: 'Slight rain showers',
  81: 'Moderate rain showers',
  82: 'Violent rain showers',
  85: 'Slight snow showers',
  86: 'Heavy snow showers',
  95: 'Thunderstorm',
  96: 'Thunderstorm with slight hail',
  99: 'Thunderstorm with heavy hail'
};

// ─── STATE & REACTIVE LAYOUT FLAG ────────────────────────────────────────────
const dateText = ref('');
const timeText = ref('');
const weatherData = ref(null);
const errorMsg = ref('');
const isMobile = ref(window.innerWidth <= MOBILE_BREAKPOINT);
let timer = null;

// ─── UPDATE isMobile WHEN RESIZING ───────────────────────────────────────────
function updateIsMobile() {
  isMobile.value = window.innerWidth <= MOBILE_BREAKPOINT;
}

// ─── COMPUTED STYLES ──────────────────────────────────────────────────────────
const containerStyle = computed(() => ({
  position: 'absolute',
  left: '50%',
  top: isMobile.value ? '15%' : '20%',
  transform: 'translate(-50%, -50%)',
  width: isMobile.value ? '90%' : `${CARD_WIDTH}px`,
  height: `${CARD_HEIGHT}px`,
  backdropFilter: 'blur(10px)',
  WebkitBackdropFilter: 'blur(10px)',
  backgroundColor: 'rgba(255, 255, 255, 0.2)',
  padding: isMobile.value ? '12px' : '16px',
  border: '1px solid rgba(255, 255, 255, 0.3)',
  borderRadius: '12px',
  boxShadow: '0 4px 16px rgba(0,0,0,0.15)',
  backdropClip: 'padding-box',
  cursor: 'default',
  userSelect: 'none'
}));

const dateStyle = computed(() => ({
  fontSize: isMobile.value ? '1rem' : '1.4rem',
  fontWeight: 'bold',
  margin: '4px 0',
}));

const timeStyle = computed(() => ({
  fontFamily: "'DSEG7Classic', monospace",
  fontSize: isMobile.value ? '2rem' : '3rem',
  letterSpacing: '0.05em',
  color: '#fff',            // ← changed to white
  margin: '4px 0',
}));

const weatherStyle = computed(() => ({
  fontSize: isMobile.value ? '1rem' : '1.2rem',
  margin: '4px 0',
  fontStyle: 'italic',
}));

// ─── TIME & WEATHER LOGIC ────────────────────────────────────────────────────
function updateTime() {
  const now = new Date();

  const month = String(now.getMonth() + 1).padStart(2, '0');
  const day = String(now.getDate()).padStart(2, '0');
  const weekday = now.toLocaleDateString('en-US', { weekday: 'long' });
  dateText.value = `${month} - ${day} ${weekday}`;

  const hours = String(now.getHours()).padStart(2, '0');
  const minutes = String(now.getMinutes()).padStart(2, '0');
  const seconds = String(now.getSeconds()).padStart(2, '0');
  timeText.value = `${hours}:${minutes}:${seconds}`;
}

function codeToDesc(code) {
  return WEATHER_CODE_MAP[code] || 'Unknown';
}

onMounted(() => {
  updateTime();
  timer = setInterval(updateTime, 1000);

  window.addEventListener('resize', updateIsMobile);

  if (navigator.geolocation) {
    navigator.geolocation.getCurrentPosition(
        (pos) => {
          const { latitude, longitude } = pos.coords;
          fetch(
              `https://api.open-meteo.com/v1/forecast?latitude=${latitude}&longitude=${longitude}&current_weather=true&timezone=auto`
          )
              .then((res) => res.json())
              .then((data) => {
                data.current_weather.weathercodeDesc = codeToDesc(
                    data.current_weather.weathercode
                );
                weatherData.value = data;
              })
              .catch((err) => {
                errorMsg.value = 'Failed to retrieve weather data';
                console.error(err);
              });
        },
        (err) => {
          errorMsg.value = 'Unable to obtain location';
          console.error(err);
        }
    );
  } else {
    errorMsg.value = 'Geolocation is not supported by this browser';
  }
});

onBeforeUnmount(() => {
  clearInterval(timer);
  window.removeEventListener('resize', updateIsMobile);
});
</script>

<style scoped>
@font-face {
  font-family: 'DSEG7Classic';
  src: url('/fonts/DSEG7ClassicMini-Regular.woff2') format('woff2'),
  url('/fonts/DSEG7ClassicMini-Regular.woff') format('woff');
  font-weight: normal;
  font-style: normal;
}

.time-weather p {
  color: #fff;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.6);
}

</style>
