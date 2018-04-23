<template>
  <div id="app">
    <div class="search-area">
      <span class="icon"><i class="fas fa-map-marker"></i></span>
      <input class="city-input" type="text" @keyup.enter="fetchData(currentCity)" v-model="currentCity" title="inputBox">
    </div>
    <div class="weather-summary">
      <img class="current-image" :src="image" alt="image">
      <div class="current-temperatures">
        <div class="current-status">{{ currentStatus }}</div>
        <div class="minmaxtemp">
          <p class="current-temp">{{ currentTemp }}</p>
          <div class="minmaxcontainer">
            <p class="maxtemp"><i class="fas fa-arrow-up"></i> {{ maxTempToday }}</p>
            <p class="mintemp"><i class="fas fa-arrow-down"></i> {{ minTempToday }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CurrentWeather',
  data () {
    return {
      currentCity: 'Zoetermeer',
      currentTemp: '13.6',
      currentStatus: '',
      image: this.getImgUrl('halfbewolkt'),
      maxTempToday: '',
      minTempToday: '',
      weatherData: null
    }
  },
  methods: {
    fetchData: function (city) {
      fetch('http://weerlive.nl/api/json-10min.php?locatie=' + city)
        .then(response => response.json())
        .then(json => {
          this.weatherData = json
          this.setData(this.weatherData)
        })
    },
    setData: function (data) {
      data = data.liveweer[0]
      this.currentTemp = Math.round(data.temp) + '°'
      this.image = this.getImgUrl(data.image)
      this.currentStatus = data.samenv
      this.minTempToday = data.d0tmin
      this.maxTempToday = data.d0tmax
    },
    getImgUrl: function (status) {
      let image = require.context('../assets/images/', false, /\.svg$/)
      return image('./' + status + '.svg')
    }
  },
  mounted () {
    // this.fetchData('Zoetermeer')
  }
}
</script>

<style scoped>

</style>
