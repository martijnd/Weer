<template>
  <div id="app">
    <div class="search-area">
      <span class="icon"><i class="fas fa-map-marker-alt"></i></span>
      <input class="city-input" type="text" @keyup.enter="fetchData(currentCity)" v-model="currentCity" title="inputBox">
    </div>
    <transition name="fade">
    <div v-if="show" class="weather-summary">
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
    </transition>
    <transition name="fade">
      <div v-if="show" class="verwachting">{{ forecast }}</div>
    </transition>

    <div class="weather-info-container">
      <transition name="fade">
      <div v-if="show" class="today">
        <h3>Meer informatie</h3>
            <p class="info-item"><span class="key">Luchtvochtigheid</span><span class="value">{{ humidity }}</span></p>
            <p class="info-item"><span class="key">Windrichting</span><span class="value">{{ windDirection }}</span></p>
            <p class="info-item"><span class="key">Windsnelheid</span><span class="value">{{ windSpeed }}</span></p>
            <p class="info-item"><span class="key">Zonsopgang</span><span class="value">{{ sunUptime }}</span></p>
            <p class="info-item"><span class="key">Zonsondergang</span><span class="value">{{ sunDowntime }}</span></p>
            <div style="clear: both"></div>
      </div>
      </transition>
    </div>
  </div>
</template>

<script>
export default {
  name: 'CurrentWeather',
  data () {
    return {
      show: false,
      currentCity: 'Zoetermeer',
      currentTemp: '14',
      currentStatus: 'Licht bewolkt',
      forecast: 'Bewolkt, plaatselijk wat regen',
      image: this.getImgUrl('halfbewolkt'),
      maxTempToday: '13',
      minTempToday: '10',
      weatherData: null,
      humidity: '70%',
      windDirection: 'ZW',
      windSpeed: '21.6 km/uur',
      sunUptime: '06:25',
      sunDowntime: '20:56'
    }
  },
  methods: {
    fetchData: function (city) {
      this.show = false

      fetch('https://weerlive.nl/api/json-10min.php?locatie=' + city)
        .then(response => response.json())
        .then(json => {
          this.weatherData = json
          this.setData(this.weatherData)
          this.show = true
        })
    },
    setData: function (data) {
      data = data.liveweer[0]
      this.currentTemp = Math.round(data.temp) + '°'
      this.image = this.getImgUrl(data.image)
      this.currentStatus = data.samenv
      this.forecast = data.verw
      this.minTempToday = data.d0tmin
      this.maxTempToday = data.d0tmax
      this.humidity = data.lv + '%'
      this.windDirection = data.windr
      this.windSpeed = data.windkmh + ' km/uur'
      this.sunUptime = data.sup
      this.sunDowntime = data.sunder
    },
    getImgUrl: function (status) {
      let image = require.context('../assets/images/', false, /\.svg$/)
      return image('./' + status + '.svg')
    }
  },
  mounted () {
    this.fetchData('Zoetermeer')
    this.show = true
  }
}
</script>

<style scoped>

</style>
