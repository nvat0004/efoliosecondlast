<template>
  <div class="container">
    <div class="header">
      <h1>Weather App</h1>
      <div class="search-bar">
        <input type="text" v-model="city" placeholder="Enter the city name" class="search-input" />
        <button @click="searchByCity" class="search-button">Search</button>
      </div>
      <!-- close the div tag for search-bar -->
    </div>
    <!-- close the div tag for header -->

    <main>
      <div v-if="weatherData">
        <h2>{{ weatherData.name }}, {{ weatherData.sys.country }}</h2>
        <div>
          <img :src="iconUrl" alt="Weather Icon" />
          <p>{{ temperature }} °C</p>
        </div>
        <span>{{ weatherData.weather[0].description }}</span>
      </div>
    </main>
  </div>
  <!-- close the main container div -->
</template>

<script>
import axios from 'axios'

const apikey = 'bc9c719b80beb9aec24414b3acc0632e'

export default {
  name: 'WeatherView',
  data() {
    return {
      city: '',
      weatherData: null
    }
  },
  computed: {
    temperature() {
      return this.weatherData ? Math.floor(this.weatherData.main.temp - 273) : null
    },
    iconUrl() {
      return this.weatherData
        ? `http://api.openweathermap.org/img/w/${this.weatherData.weather[0].icon}.png`
        : null
    }
  },
  mounted() {
    this.fetchCurrentLocationWeather()
  },
  methods: {
    async fetchCurrentLocationWeather() {
      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(async (position) => {
          const { latitude, longitude } = position.coords
          const url = `http://api.openweathermap.org/data/2.5/weather?lat=${latitude}&lon=${longitude}&appid=${apikey}`
          await this.fetchWeatherData(url)
        })
      }
    },
    async fetchWeatherData(url) {
      try {
        const response = await axios.get(url)
        this.weatherData = response.data
      } catch (error) {
        console.error('Error fetching weather data:', error)
      }
    }
  }
}
</script>
