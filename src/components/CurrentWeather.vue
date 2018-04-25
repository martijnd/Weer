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
      <div v-if="show" class="forecast">{{ forecast }}</div>
    </transition>

    <transition name="fade">
    <div v-if="show" class="weather-info-container">
      <div class="tomorrow">
        <h4>Morgen</h4>
        <div class="info-item"><span class="key">Max. temperatuur</span><span class="value">{{ d1tempmax }}</span></div>
        <div class="info-item"><span class="key">Min. temperatuur</span><span class="value">{{ d1tempmin }}</span></div>
        <div class="info-item"><span class="key">Zonkans</span><span class="value">{{ d1zon }}</span></div>
        <div class="info-item"><span class="key">Neerslagkans</span><span class="value">{{ d1neerslag }}</span></div>
      </div>

      <div class="today">
        <h3>Meer informatie</h3>
            <div class="info-item"><span class="key">Neerslagkans</span><span class="value">{{ rainchance }}</span></div>
            <div class="info-item"><span class="key">Luchtvochtigheid</span><span class="value">{{ humidity }}</span></div>
            <div class="info-item"><span class="key">Windrichting</span><span class="value">{{ windDirection }}</span></div>
            <div class="info-item"><span class="key">Windsnelheid</span><span class="value">{{ windSpeed }}</span></div>
            <div class="info-item"><span class="key">Zonsopgang</span><span class="value">{{ sunUptime }}</span></div>
            <div class="info-item"><span class="key">Zonsondergang</span><span class="value">{{ sunDowntime }}</span></div>
            <!--<div style="clear: both"></div>-->
      </div>

      <div class="tomorrow">
        <h4>Overmorgen</h4>
        <div class="info-item"><span class="key">Max. temperatuur</span><span class="value">{{ d2tempmax }}</span></div>
        <div class="info-item"><span class="key">Min. temperatuur</span><span class="value">{{ d2tempmin }}</span></div>
        <div class="info-item"><span class="key">Zonkans</span><span class="value">{{ d2zon }}</span></div>
        <div class="info-item"><span class="key">Neerslagkans</span><span class="value">{{ d2neerslag }}</span></div>
      </div>
    </div>
    </transition>
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

      d1tempmax: '14°C',
      d1tempmin: '7°C',
      d1zon: '50%',
      d1neerslag: '0%',

      maxTempToday: '13',
      minTempToday: '10',
      weatherData: null,
      rainchance: '10%',
      humidity: '70%',
      windDirection: 'ZW',
      windSpeed: '21.6 km/uur',
      sunUptime: '06:25',
      sunDowntime: '20:56',

      d2tempmax: '14°C',
      d2tempmin: '7°C',
      d2zon: '20%',
      d2neerslag: '2%'
    }
  },
  methods: {
    setTitle (currentCity) {
      document.title = 'Het weer van ' + currentCity
    },
    fetchData: function (city) {
      this.show = false
      fetch('https://weerlive.nl/api/json-10min.php?locatie=' + city)
        .then(response => response.json())
        .then(json => {
          this.weatherData = json
          this.setData(this.weatherData)
          setTimeout(() => {
            this.show = true
          }, 800)
        })
    },
    setData: function (data) {
      data = data.liveweer[0]
      this.currentCity = data.plaats
      this.setTitle(this.currentCity)
      this.currentTemp = Math.round(data.temp) + '°C'
      this.image = this.getImgUrl(data.image)
      this.currentStatus = data.samenv
      this.forecast = data.verw

      // morgen
      this.d1tempmax = data.d1tmax + '°C'
      this.d1tempmin = data.d1tmin + '°C'
      this.d1zon = data.d1zon + '%'
      this.d1neerslag = data.d1neerslag + '%'

      // vandaag
      this.minTempToday = data.d0tmin
      this.maxTempToday = data.d0tmax
      this.rainchance = data.d0neerslag + '%'
      this.humidity = data.lv + '%'
      this.windDirection = data.windr
      this.windSpeed = data.windkmh + ' km/uur'
      this.sunUptime = data.sup
      this.sunDowntime = data.sunder

      // overmorgen
      this.d2tempmax = data.d2tmax + '°C'
      this.d2tempmin = data.d2tmin + '°C'
      this.d2zon = data.d2zon + '%'
      this.d2neerslag = data.d2neerslag + '%'
    },
    getImgUrl: function (status) {
      let image = require.context('../assets/images/', false, /\.svg$/)
      return image('./' + status + '.svg')
    }
  },
  mounted () {
    this.fetchData('Zoetermeer')
    setTimeout(() => {
      this.show = true
    }, 200)
  }
}
</script>

<style scoped>

</style>
