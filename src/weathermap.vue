<template>
  <div class="weather-app">
    <h1 class="app-title">Weather Map</h1>
    <div class="search-container">
      <input v-model="searchQuery" placeholder="Enter city name" class="search-input" />
      <button @click="searchWeather" :disabled="loading" class="search-button">
        <i class="bi bi-search"></i> Search
      </button>
    </div>
    <div v-if="loading" class="loading-indicator">
      <i class="bi bi-hourglass-bottom"></i> Loading...
    </div>
    <div v-if="showEmptyQuery" class="error-message">Please enter a city name</div>
    <div v-if="data" id="dataDiv" class="weather-info">
      <div class="info-group location-info">
        <h2><i class="bi bi-geo-alt"></i> {{ data.name }}</h2>
        <p><i class="bi bi-compass"></i> Coordinates: Longitude {{ data.coord.lon }}, Latitude {{ data.coord.lat }}</p>
      </div>
      <div class="info-group current-weather">
        <p><i class="bi bi-cloud"></i> Today's Forecast: {{ data.weather[0].description }}</p>
        <img v-bind:src="'http://openweathermap.org/img/wn/' + data.weather[0].icon + '.png'" alt="Weather Icon" />
        <p><i class="bi bi-thermometer"></i> Temperature: {{ convertKelvinToCelsius(data.main.temp) }}°C</p>
        <p><i class="bi bi-bar-chart"></i> Atmospheric Pressure: {{ data.main.pressure }} hPa</p>
        <p><i class="bi bi-droplet"></i> Humidity: {{ data.main.humidity }}%</p>
      </div>
      <div class="info-group wind-info">
        <p><i class="bi bi-wind"></i> Wind Speed: {{ data.wind.speed }} m/s</p>
        <p><i class="bi bi-arrow-down-up"></i> Wind Direction: {{ data.wind.deg }}°</p>
        <p><i class="bi bi-speedometer"></i> Gust Speed: {{ data.wind.gust }} m/s</p>
      </div>
    </div>
    <div v-if="showErrorMessage" class="error-message">Information not available for {{ searchQuery }}</div>
  </div>
</template>

<script>


export default { 
  data() {
    return {
      data: null,
      searchQuery: '',
      loading: false,
      showErrorMessage: false,
      showEmptyQuery: false,
    };
  },


  
  watch: {
    searchQuery() {
      this.showErrorMessage = false;
      this.showEmptyQuery = false;
    },
  },

  methods: {
    async searchWeather() {
      if (this.searchQuery.trim() === '') {
        this.showEmptyQuery = true;
        return;
      }

      this.loading = true;
      try {
        const response = await fetch(`https://api.openweathermap.org/data/2.5/weather?q=${this.searchQuery}&appid=${import.meta.env.VITE_API_KEY}&units=metrics`);
        if (response.ok) {
          this.data = await response.json();
          this.showErrorMessage = false;
        } else {
          this.data = null;
          this.showErrorMessage = true;
        }
      } catch (error) {
        console.error('Error fetching data:', error);
      } finally {
        this.loading = false;
      }
    },
    convertKelvinToCelsius(kelvin) {
      return (kelvin - 273.15).toFixed(2);
    },
    
  },

  
};
</script>

<style scoped>
.weather-app {
  max-width: 800px;
  margin: 0 auto;
  padding: 20px;
  background-color: #f4f4f4;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.app-title {
  font-family: 'CNN Sans', sans-serif;
  font-size: 32px;
  color: #333;
  margin-bottom: 20px;
  text-align: center;
}

.search-container {
  display: flex;
  align-items: center;
  margin-bottom: 20px;
}

.search-input {
  flex: 1;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 5px 0 0 5px;
}

.search-button {
  padding: 10px;
  border: 1px solid #333;
  background-color: #333;
  color: #fff;
  border-radius: 0 5px 5px 0;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.search-button:hover {
  background-color: #555;
}

.loading-indicator {
  text-align: center;
  margin-top: 20px;
  color: #555;
}

#dataDiv {
  background-color: #fff;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
}

.info-group {
  flex: 1;
}

h2 {
  font-family: 'CNN Sans', sans-serif;
  font-size: 28px;
  margin-bottom: 10px;
  color: #333;
}

p {
  font-size: 16px;
  margin-bottom: 8px;
  color: #555;
}

.error-message {
  color: #cc0000;
  margin-top: 20px;
  text-align: center;
}
</style>
