<template>
  <div id="main" :class="isDay ? 'day' : 'night'">
    <div class="container my-5" style="max-width: 400px; min-width: 360px">
      <h1 class="title text-center">Weather App</h1>
      <form class="search-location" v-on:submit.prevent="getWeather">
        <input
          type="text"
          class="form-control text-muted form-rounded p-4 shadow-sm"
          placeholder="Search.."
          v-model="citySearch"
          autocomplete="off"
          style="font-weight: 500"
        />
      </form>
      <p class="text-center my-3" v-if="cityFound">No city found</p>

      <div class="card rounded my-3 shadow-lg back-card overflow-hidden">
        <!-- weather animation container -->
        <div>
          <div icon="sunny" v-if="clearSky" data-label="Sunny">
            <span class="sun"></span>
          </div>

          <div icon="snowy" v-if="snowy" data-label="Chilly">
            <ul>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
            </ul>
          </div>

          <div icon="stormy" v-if="stormy" data-label="Soggy">
            <span class="cloud"></span>
            <ul>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
              <li></li>
            </ul>
          </div>
          <div icon="cloudy" v-if="cloudy" data-label="Perfect">
            <span class="cloud"></span>
            <span class="cloud"></span>
          </div>
          <div icon="nightmoon" v-if="clearNight" data-label="Cool!">
            <span class="moon"></span>]
            <span class="meteor"></span>
          </div>
        </div>

        <div class="card-top text-center" style="margin-botton: 15rem">
          <div class="city-name my-3">
            <p>{{ weather.cityName }}</p>
            <span> . . . </span>
            <p class="">{{ weather.country }}</p>
          </div>
        </div>

        <div class="card-body">
          <div class="card-mid">
            <div class="row">
              <div class="col-12 text-center temp">
                <span> {{ weather.temperature }}&deg;C </span>
                <p class="my-4">{{ weather.description }}</p>
              </div>
            </div>
          </div>
        </div>
        <div class="row">
          <div class="col d-flex justify-content-between px-5 mx-5">
            <p>
              <img src="./assets/down.svg" alt="" />
              {{ weather.lowTemp }}&deg;C
            </p>
            <p>
              <img src="./assets/up.svg" alt="" />
              {{ weather.highTemp }}&deg;C
            </p>
          </div>
        </div>
        <div class="card-bottom px-5 py-4 row">
          <div class="col text-center">
            <p>{{ weather.feelsLike }}&deg;C</p>
            <span>Feels Like</span>
          </div>
          <div class="col text-center">
            <p>{{ weather.humidity }}%</p>
            <span>Humidity</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cityFound: false,
      visible: false,
      stormy: false,
      cloudy: false,
      clearSky: false,
      clearNight: false,
      snowy: false,
      isDay: true,
      citySearch: "",

      weather: {
        cityName: "Phagwara",
        country: "NG",
        temperature: 12,
        description: "Clouds everywhere",
        lowTemp: "19",
        highTemp: "30",
        feelsLike: "20",
        humidity: "55",
      },
    };
  },
  methods: {
    getWeather: async function () {
      console.log(this.citySearch);
      const key = "4fc0db0dfb60e1db34f2b956fd3010dd";
      const baseURL = `https://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&appid=${key}&units=metric`;
      //fetch weather
      try {
        const response = await fetch(baseURL);
        const data = await response.json();
        console.log(data);
        this.citySearch = "";
        this.weather.cityName = data.name;
        console.log(this.weather.cityName);
        this.weather.country = data.sys.country;
        this.weather.temperature = Math.round(data.main.temp);
        this.weather.description = data.weather[0].description;
        this.weather.lowTemp = Math.round(data.main.temp_min);
        this.weather.highTemp = Math.round(data.main.temp_max);
        this.weather.feelsLike = Math.round(data.main.feels_like);
        this.weather.humidity = Math.round(data.main.humidity);
        const timeOfDay = data.weather[0].icon;
        ///check for time of day
        if (timeOfDay.includes("n")) {
          this.isDay = false;
        } else {
          this.isDay = true;
        }
        const mainWeather = data.weather[0].main;
        //check weather animations
        if (mainWeather.includes("Clouds")) {
          this.stormy = false;
          this.cloudy = true;
          this.clearSky = false;
          this.clearNight = false;
          this.snowy = false;
        }

        if (
          mainWeather.includes("Thunderstorm") ||
          mainWeather.includes("Rain")
        ) {
          this.stormy = true;
          this.cloudy = false;
          this.clearSky = false;
          this.clearNight = false;
          this.snowy = false;
        }
        if (mainWeather.includes("Clear") && this.isDay) {
          this.stormy = false;
          this.cloudy = false;
          this.clearSky = true;
          this.clearNight = false;
          this.snowy = false;
        }
        if (mainWeather.includes("Clouds") && !this.isDay) {
          this.stormy = false;
          this.cloudy = false;
          this.clearSky = false;
          this.clearNight = true;
          this.snowy = false;
        }
        if (mainWeather.includes("Snow")) {
          this.stormy = false;
          this.cloudy = false;
          this.clearSky = false;
          this.clearNight = false;
          this.snowy = true;
        }
        this.visible = true;
        this.cityFound = false;
      } catch (error) {
        console.log(error);
        this.cityFound = true;
        this.visible = false;
      }
    },
  },
};
</script>

<style>
@import "./assets/animation.css";
@import "./assets/custom.css";
</style>
