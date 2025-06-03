<!-- src/components/TimeCard.vue -->
<template>
  <div class="time-weather">
    <p class="date-text">{{ dateText }}</p>
    <p class="time-text">{{ timeText }}</p>

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
import { ref, onMounted, onUnmounted } from 'vue';

// ─── CONSTANTS ────────────────────────────────────────────────────────────────
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
const dateText = ref('');
const timeText = ref('');
const weatherData = ref(null);
const errorMsg = ref('');

let timer = null;

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

// ─── LIFECYCLE HOOKS ─────────────────────────────────────────────────────────
onMounted(() => {
  updateTime();
  timer = setInterval(updateTime, 1000);

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
/* ─── CONTAINER ──────────────────────────────────────────────────────────────── */
.time-weather {
  position: absolute;
  left: 50%;
  top: 20%;
  transform: translate(-50%, -50%);
  width: 90%;
  max-width: 600px;
  backdrop-filter: blur(10px);
  -webkit-backdrop-filter: blur(10px);
  background-color: rgba(255, 255, 255, 0.2);
  padding: 16px;
  border: 1px solid rgba(255, 255, 255, 0.3);
  border-radius: 12px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
  backdrop-clip: padding-box;
  cursor: default;
  user-select: none;
}

/* ─── FONTS ─────────────────────────────────────────────────────────────────── */
@font-face {
  font-family: 'DSEG7Classic';
  src: url('/fonts/DSEG7ClassicMini-Regular.woff2') format('woff2'),
  url('/fonts/DSEG7ClassicMini-Regular.woff') format('woff');
  font-weight: normal;
  font-style: normal;
}

.time-weather p {
  margin: 6px 0;
  color: #fff;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.6);
}

/* ─── DATE / TIME TEXT ───────────────────────────────────────────────────────── */
.date-text {
  font-size: 1.4rem;
  font-weight: bold;
}

.time-text {
  font-family: 'DSEG7Classic', monospace;
  font-size: 3rem;
  letter-spacing: 0.05em;
  color: #0ff;
}

/* ─── WEATHER / ERROR / LOADING ─────────────────────────────────────────────── */
.weather-text {
  font-size: 1.2rem;
}

.error-text,
.loading-text {
  font-size: 1.2rem;
  font-style: italic;
}

/* ─── RESPONSIVE ADJUSTMENTS ─────────────────────────────────────────────────── */
/* Phones (screen width ≤ 480px): shrink font sizes */
@media (max-width: 480px) {
  .date-text {
    font-size: 1.1rem;
  }
  .time-text {
    font-size: 2rem;
  }
  .weather-text,
  .error-text,
  .loading-text {
    font-size: 1rem;
  }
}

/* Small tablets (481px – 768px): moderately smaller fonts */
@media (min-width: 481px) and (max-width: 768px) {
  .date-text {
    font-size: 0.96rem;
  }
  .time-text {
    font-size: 2rem;
  }
  .weather-text,
  .error-text,
  .loading-text {
    font-size: 1.1rem;
  }
}

/* when screen height ≤ 1200px, make card and font smaller */
@media (max-height: 1200px) {
  .time-weather {
    padding: 12px;
    max-width: 400px;
  }
  .date-text {
    font-size: 0.96rem;
  }
  .time-text {
    font-size: 2rem;
  }
  .weather-text,
  .error-text,
  .loading-text {
    font-size: 1.1rem;
  }
}

/* ─── LARGE‐SCREEN ADJUSTMENTS (screen width ≥ 1920px) ───────────────────────────────────── */
@media (min-width: 1920px) {
  .time-weather {
    max-width: 800px;
    padding: 24px;
  }
  .date-text {
    font-size: 2rem;
  }
  .time-text {
    font-size: 4rem;
  }
  .weather-text,
  .error-text,
  .loading-text {
    font-size: 1.5rem;
  }
}
</style>
