<template>
  <div class="weather-app container-fluid">
    <h1 class="app-title text-center">Weather Map</h1>
    <div class="search-container d-flex justify-content-center align-items-center mb-4">
      <input v-model="searchQuery" placeholder="Enter city name" class="form-control search-input mr-2" @keyup.enter="searchWeather" />
      <button @click="searchWeather" :disabled="loading" class="btn btn-primary search-button">
        <i class="bi bi-search"></i> Search
      </button>
    </div>
    <div v-if="loading" class="loading-indicator text-center">
      <i class="bi bi-hourglass-bottom"></i> Loading...
    </div>
    <div v-if="showEmptyQuery" class="error-message text-center">Please enter a city name</div>
    <div v-if="data" id="dataDiv" class="weather-info">
      <div class="location-info">
        <h2><i class="bi bi-geo-alt"></i> {{ data.name ? data.name : 'N/A' }}</h2>
        <p><i class="bi bi-compass"></i> Coordinates: Longitude {{ data.coord.lon ? data.coord.lon : 'N/A' }}, Latitude {{ data.coord.lat ? data.coord.lat : 'N/A' }}</p>
      </div>
      <div class="current-weather">
        <p><i class="bi bi-cloud"></i> Today's Forecast: {{ data.weather[0].description ? data.weather[0].description : 'N/A' }}</p>
        <img v-if="data.weather[0].icon" v-bind:src="'http://openweathermap.org/img/wn/' + data.weather[0].icon + '.png'" alt="Weather Icon" />
        <p><i class="bi bi-thermometer"></i> Temperature: {{ data.main.temp ? convertKelvinToCelsius(data.main.temp) + '°C' : 'N/A' }}</p>
        <p><i class="bi bi-bar-chart"></i> Atmospheric Pressure: {{ data.main.pressure ? data.main.pressure + ' hPa' : 'N/A' }}</p>
        <p><i class="bi bi-droplet"></i> Humidity: {{ data.main.humidity ? data.main.humidity + '%' : 'N/A' }}</p>
      </div>
      <div class="wind-info">
        <p><i class="bi bi-wind"></i> Wind Speed: {{ data.wind.speed ? data.wind.speed + ' m/s' : 'N/A' }}</p>
        <p><i class="bi bi-arrow-down-up"></i> Wind Direction: {{ data.wind.deg ? data.wind.deg + '°' : 'N/A' }}</p>
        <p><i class="bi bi-speedometer"></i> Gust Speed: {{ data.wind.gust ? data.wind.gust + ' m/s' : 'N/A' }}</p>
      </div>
    </div>
    <div v-if="showErrorMessage" class="error-message text-center">Information not available for {{ searchQuery }}</div>
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
body{
  font-family: 'Roboto', sans-serif; 
  overflow: hidden;
}
.weather-app {
  max-width: 800px;
  margin: 0 auto;
  margin-top: 20px;
  padding: 20px;
  background-color: #e0f2f1;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.app-title {
  font-family: 'Roboto', sans-serif;
  font-size: 32px;
  color: #009688; 
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
  border: 1px solid #009688; 
  border-radius: 5px 0 0 5px; 
  border-right: none; 
}

.search-button {
  border: 1px solid #009688; 
  background-color: #009688; 
  color: #fff; 
  padding: 10px 20px;
  border-radius: 0 5px 5px 0; 
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.search-button:hover {
  background-color: #00796b;
}


.loading-indicator {
  text-align: center;
  margin-top: 20px;
  color: #555;
}

.weather-info {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 20px;
}

.location-info,
.current-weather,
.wind-info {
  background-color: #fff;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}

h2 {
  font-family: 'CNN Sans', sans-serif;
  font-size: 24px;
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
