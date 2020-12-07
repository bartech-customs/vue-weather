<template>
  <q-page class="flex column q-px-xl q-pt-lg align-center" :class="bgClass">
    <q-input
      placeholder="Search"
      v-model="search"
      @keyup.enter="getSearch"
      dark
      borderless
    >
      <template v-slot:before>
        <q-icon @click="getLocation" name="my_location" />
      </template>

      <template v-slot:append>
        <q-btn @click="getSearch" round dense icon="search" />
      </template>
    </q-input>

    <!-- Weather Data -->
    <template v-if="weatherData">
      <div class="col  text-white text-center column justify-center  ">
        <div class="text-h4 text-weight-light">{{ weatherData.name }}</div>
        <div class="text-h6 text-weight-light">
          {{ weatherData.weather[0].main }}
        </div>
        <div class="text-h1 text-weight-thin q-my-lg relative-position">
          <span>{{ Math.round(weatherData.main.temp) }}</span
          ><span class="text-h4 relative-position degree">&deg; C</span>
        </div>
      </div>
      <div class="col text-center text-white">
        <img
          :src="
            `http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`
          "
          alt="weather image"
        />
      </div>
    </template>
    <template v-else>
      <div
        class="col text-center text-white column  content-center justify-center "
      >
        <div class="text-h2 text-weight-thin">
          Quasar <br />
          Weather
        </div>
        <q-btn
          @click="getLocation"
          flat
          style="min-width: 30% "
          class="q-mt-lg q-py-sm"
        >
          <q-icon left size="3em" name="my_location" />
          <div class="text-weight-light">Find My Location</div>
        </q-btn>
      </div>
    </template>
  </q-page>
</template>

<script>
export default {
  name: "PageIndex",
  data() {
    return {
      search: "",
      weatherData: null,
      lat: null,
      lon: null,
      apiUrl: "https://api.openweathermap.org/data/2.5/weather",
      apiKey: "<YOUR API KEY>"
    };
  },
  computed: {
    bgClass() {
      if (this.weatherData) {
        if (this.weatherData.weather[0].icon.endsWith("n")) {
          return "bg-night";
        } else {
          return "bg-day";
        }
      }
    }
  },
  methods: {
    getLocation() {
      this.$q.loading.show();
      navigator.geolocation.getCurrentPosition(position => {
        this.lat = position.coords.latitude;
        this.lon = position.coords.longitude;
        this.getWeatherByCoords();
      });
    },
    getWeatherByCoords() {
      this.$q.loading.show();
      this.$axios(
        `${this.apiUrl}?lat=${this.lat}&lon=${this.lon}&appid=${this.apiKey}&units=metric`
      ).then(response => {
        this.weatherData = response.data;
        this.$q.loading.hide();
      });
    },
    getSearch() {
      this.$axios(
        `${this.apiUrl}?q=${this.search}&appid=${this.apiKey}&units=metric`
      ).then(response => (this.weatherData = response.data));
    }
  }
};
</script>

<style lang="scss">
.q-page {
  background: #0f2027; /* fallback for old browsers */
  background: -webkit-linear-gradient(
    to top,
    #2c5364,
    #203a43,
    #0f2027
  ); /* Chrome 10-25, Safari 5.1-6 */
  background: linear-gradient(
    to top,
    #2c5364,
    #203a43,
    #0f2027
  ); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
  &.bg-night {
    background: #3c3b3f; /* fallback for old browsers */
    background: -webkit-linear-gradient(
      to bottom,
      #605c3c,
      #3c3b3f
    ); /* Chrome 10-25, Safari 5.1-6 */
    background: linear-gradient(
      to bottom,
      #605c3c,
      #3c3b3f
    ); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
  }
  &.bg-day {
    background: #70e1f5; /* fallback for old browsers */
    background: -webkit-linear-gradient(
      to bottom,
      #ffcd8d,
      #70e1f5
    ); /* Chrome 10-25, Safari 5.1-6 */
    background: linear-gradient(
      to bottom,
      #fcc782,
      #70e1f5
    ); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
  }
}
.degree {
  top: -45px;
}
</style>
