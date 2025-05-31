< !-- src/components/TimeCard.vue -->
<template>
  <div
      class="time-weather"
      :style="containerStyle"
      @mousedown.prevent="startDrag"
  >
    <p class="time-text">{{ currentTime }}</p>

    <div v-if="weatherData">
      <p class="weather-text">
        Weather: {{ weatherData.current_weather.weathercodeDesc }},
        Temperature: {{ weatherData.current_weather.temperature }} ℃
      </p>
    </div>

    <p v-else-if="errorMsg" class="error-text">{{ errorMsg }}</p>
    <p v-else class="loading-text">Retrieving weather information...</p>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue';

// ─── CONSTANTS ────────────────────────────────────────────────────────────────
const CARD_WIDTH = 800;
const CARD_HEIGHT = 200;

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

// ─── REACTIVE STATE ──────────────────────────────────────────────────────────
const currentTime = ref('');
const weatherData = ref(null);
const errorMsg = ref('');

const dragging = ref(false);
const position = ref({ x: 0, y: 0 });
const dragOffset = { x: 0, y: 0 };

let timer = null;

// ─── COMPUTED STYLES ──────────────────────────────────────────────────────────
const containerStyle = computed(() => ({
  position: 'absolute',
  left: `${position.value.x}px`,
  top: `${position.value.y}px`,
  width: `${CARD_WIDTH}px`,
  backdropFilter: 'blur(10px)',
  WebkitBackdropFilter: 'blur(10px)',
  backgroundColor: 'rgba(255, 255, 255, 0.2)',
  padding: '16px',
  border: '1px solid rgba(255, 255, 255, 0.3)',
  borderRadius: '12px',
  boxShadow: '0 4px 16px rgba(0,0,0,0.15)',
  backdropClip: 'padding-box',
  cursor: dragging.value ? 'grabbing' : 'grab',
  userSelect: 'none'
}));

// ─── FUNCTIONS ────────────────────────────────────────────────────────────────
function updateTime() {
  const now = new Date();

  const month = String(now.getMonth() + 1).padStart(2, '0');
  const day = String(now.getDate()).padStart(2, '0');

  const hours = String(now.getHours()).padStart(2, '0');
  const minutes = String(now.getMinutes()).padStart(2, '0');
  const seconds = String(now.getSeconds()).padStart(2, '0');
  const timePart = `${hours}:${minutes}:${seconds}`;

  const weekday = now.toLocaleDateString('en-US', { weekday: 'long' });

  currentTime.value = `Date: ${month}/${day}    Time: ${timePart}    Weekday: ${weekday}`;
}

function codeToDesc(code) {
  return WEATHER_CODE_MAP[code] || 'Unknown';
}

function startDrag(event) {
  dragging.value = true;
  dragOffset.x = event.clientX - position.value.x;
  dragOffset.y = event.clientY - position.value.y;
  window.addEventListener('mousemove', onDrag);
  window.addEventListener('mouseup', stopDrag);
}

function onDrag(event) {
  if (!dragging.value) return;
  position.value.x = event.clientX - dragOffset.x;
  position.value.y = event.clientY - dragOffset.y;
}

function stopDrag() {
  dragging.value = false;
  window.removeEventListener('mousemove', onDrag);
  window.removeEventListener('mouseup', stopDrag);
}

// ─── LIFECYCLE HOOKS ─────────────────────────────────────────────────────────
onMounted(() => {
  // Initialize time display
  updateTime();
  timer = setInterval(updateTime, 1000);

  // Center card horizontally; place at 1/4 down vertically
  const viewportWidth = window.innerWidth;
  const viewportHeight = window.innerHeight;

  position.value.x = viewportWidth / 2 - CARD_WIDTH / 2;
  position.value.y = viewportHeight / 4 - CARD_HEIGHT / 2;

  // Fetch weather data if geolocation is available
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

onUnmounted(() => {
  clearInterval(timer);
});
</script>

<style scoped>
.time-weather p {
  margin: 6px 0;
  colour: #fff;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.6);
}

.time-text {
  font-size: 1.4rem;
  font-weight: bold;
}

.weather-text {
  font-size: 1.2rem;
}

.error-text,
.loading-text {
  font-size: 1.2rem;
  font-style: italic;
}
</style>
