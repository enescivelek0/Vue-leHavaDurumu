<template>
  <div class="weather" :class="bgClass">
    <h1>🌤️ Hava Durumu</h1>

    <!-- Arama Kutusu -->
    <div class="search-box">
      <input 
        v-model="city" 
        @keyup.enter="getWeather" 
        placeholder="Şehir adı gir (örn: Istanbul)" 
      />
      <br></br>
      <button @click="getWeather">🔍 Ara</button>
    </div>

    <!-- Hava Bilgisi -->
    <div v-if="weather" class="weather-card">
      <h2>{{ weather.name }}, {{ weather.sys.country }}</h2>
      <p>🌡️ {{ (weather.main.temp - 273.15).toFixed(1) }}°C</p>
      <p>☁️ {{ weather.weather[0].description }}</p>
      <p>💨 {{ weather.wind.speed }} m/s</p>
    </div>

    <p v-if="error" class="error">{{ error }}</p>
  </div>
</template>

<script setup lang="ts">
import { ref, computed } from "vue"
import axios from "axios"

interface WeatherData {
  name: string
  sys: { country: string }
  main: { temp: number }
  weather: { main: string; description: string }[]
  wind: { speed: number }
}

const city = ref<string>("")
const weather = ref<WeatherData | null>(null)
const error = ref<string>("")

const API_KEY = "apikeyinizi_buraya_yapistirin"
const URL = "https://api.openweathermap.org/data/2.5/weather"

async function getWeather() {
  if (!city.value) return
  try {
    const response = await axios.get<WeatherData>(URL, {
      params: {
        q: city.value,
        appid: API_KEY,
        lang: "tr"
      }
    })
    weather.value = response.data
    error.value = ""
  } catch (err) {
    error.value = "Şehir bulunamadı!"
    weather.value = null
  }
}

// Dinamik arka plan
const bgClass = computed(() => {
  if (!weather.value) return "default"

  const main = weather.value.weather[0].main.toLowerCase()

  if (main.includes("cloud")) return "cloudy"
  if (main.includes("rain") || main.includes("drizzle")) return "rainy"
  if (main.includes("snow")) return "snowy"
  if (main.includes("clear")) return "sunny"

  return "default"
})
</script>

<style>
.weather {
  max-width: 500px;
  margin: 30px auto;
  text-align: center;
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  padding: 20px;
  transition: background 0.5s ease;
}

/* Arama Kutusu */
.search-box {
  display: flex;
  justify-content: center;
  margin: 20px 0;
  flex-wrap: wrap; /* mobil uyum */
}

.search-box input {
  padding: 12px 16px;
  border: none;
  border-radius: 25px 0 0 25px;
  outline: none;
  width: 70%;
  min-width: 200px;
  font-size: 14px;
  box-shadow: 0 2px 6px rgba(0,0,0,0.2);
}

.search-box button {
  padding: 12px 20px;
  border: none;
  border-radius: 0 25px 25px 0;
  background: #42b883;
  color: white;
  font-weight: bold;
  cursor: pointer;
  transition: background 0.3s ease;
  box-shadow: 0 2px 6px rgba(0,0,0,0.2);
}

.search-box button:hover {
  background: #2c9c6a;
}

/* Hava Kartı */
.weather-card {
  background: rgba(255,255,255,0.15);
  backdrop-filter: blur(8px);
  border-radius: 12px;
  padding: 20px;
  margin-top: 20px;
  color: white;
  box-shadow: 0 4px 12px rgba(0,0,0,0.2);
  transition: transform 0.3s ease;
}

.weather-card:hover {
  transform: translateY(-4px);
}

/* Hata Mesajı */
.error {
  color: #ff6b6b;
  font-weight: bold;
  margin-top: 10px;
}

/* Arka planlar */
.default {
  background: linear-gradient(to bottom, #4facfe, #00f2fe);
}

.sunny {
  background: linear-gradient(to bottom, #fbc531, #e1b12c);
}

.cloudy {
  background: linear-gradient(to bottom, #636e72, #b2bec3);
}

.rainy {
  background: linear-gradient(to bottom, #74b9ff, #0984e3);
}

.snowy {
  background: linear-gradient(to bottom, #dfe6e9, #b2bec3);
  color: black;
}
</style>
