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
import { ref, onMounted, onUnmounted } from "vue";

// ─── CONSTANTS ────────────────────────────────────────────────────────────────
const WEATHER_CODE_MAP = {
  0: "Clear",
  1: "Mainly clear",
  2: "Partly cloudy",
  3: "Overcast",
  45: "Fog",
  48: "Depositing rime fog",
  51: "Light drizzle",
  53: "Moderate drizzle",
  55: "Dense drizzle",
  56: "Light freezing drizzle",
  57: "Dense freezing drizzle",
  61: "Slight rain",
  63: "Moderate rain",
  65: "Heavy rain",
  66: "Light freezing rain",
  67: "Heavy freezing rain",
  71: "Slight snow fall",
  73: "Moderate snow fall",
  75: "Heavy snow fall",
  77: "Snow grains",
  80: "Slight rain showers",
  81: "Moderate rain showers",
  82: "Violent rain showers",
  85: "Slight snow showers",
  86: "Heavy snow showers",
  95: "Thunderstorm",
  96: "Thunderstorm with slight hail",
  99: "Thunderstorm with heavy hail",
};

// ─── REACTIVE STATE ──────────────────────────────────────────────────────────
const dateText = ref("");
const timeText = ref("");
const weatherData = ref(null);
const errorMsg = ref("");

// ─── FUNCTIONS ────────────────────────────────────────────────────────────────
function updateTime() {
  const now = new Date();

  const month = String(now.getMonth() + 1).padStart(2, "0");
  const day = String(now.getDate()).padStart(2, "0");
  const weekday = now.toLocaleDateString("en-US", { weekday: "long" });
  dateText.value = `${month} - ${day} ${weekday}`;

  const hours = String(now.getHours()).padStart(2, "0");
  const minutes = String(now.getMinutes()).padStart(2, "0");
  const seconds = String(now.getSeconds()).padStart(2, "0");
  timeText.value = `${hours}:${minutes}:${seconds}`;
}

function codeToDesc(code) {
  return WEATHER_CODE_MAP[code] || "Unknown";
}

// ─── LIFECYCLE HOOKS ─────────────────────────────────────────────────────────
let timer = null;

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
                errorMsg.value = "Failed to retrieve weather data";
                console.error(err);
              });
        },
        (err) => {
          errorMsg.value = "Unable to obtain location";
          console.error(err);
        }
    );
  } else {
    errorMsg.value = "Geolocation is not supported by this browser";
  }
});

onUnmounted(() => {
  clearInterval(timer);
});
</script>

<style scoped>
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
  padding: 1rem;
  border: 1px solid rgba(255, 255, 255, 0.3);
  border-radius: 12px;
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.15);
  backdrop-clip: padding-box;
  cursor: default;
  user-select: none;
}

.date-text {
  margin: 0.3rem 0;
  font-size: 1.25rem;
  font-weight: bold;
  color: #fff;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.6);
}

.time-text {
  margin: 0.3rem 0;
  font-family: inherit;
  font-size: 2.5rem;
  color: #fff;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.6);
}

.weather-text,
.error-text,
.loading-text {
  margin: 0.3rem 0;
  font-size: 1rem;
  color: #fff;
  text-shadow: 0 1px 2px rgba(0, 0, 0, 0.6);
}

.error-text,
.loading-text {
  font-style: italic;
}

@media (max-width: 480px) {
  .time-weather {
    top: 25%;
    padding: 0.75rem;
    max-width: 90%;
  }
  .date-text {
    font-size: 1.1rem;
  }
  .time-text {
    font-size: 2rem;
  }
  .weather-text,
  .error-text,
  .loading-text {
    font-size: 0.9rem;
  }
}

@media (min-width: 1200px) {
  .time-weather {
    top: 18%;
  }
}
</style>
