<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 16 ? 'warm' :  ''">
    <main>
      <div class="search-box">
        <input type="text" class="search-bar" placeholder="Enter your city..." v-model="query" @keypress="fetchWeather()">
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="current-weather-box">
          <h1 class="current-title">Current Weather</h1>
          <div class="location-box">
            <div class="date">{{ dateBuilder() }}</div>
            <div class="location">{{ query | upperCase }} {{ weather.sys.country }}</div>
          </div>

          <div class="weather-box">
            <div class="temp-infos">
              <div class="temp-main">{{weather.main.temp | roundNumber }}°C</div>
              <div class="temp temp-felt">Felt: {{ weather.main.feels_like | roundNumber }}°C</div>
              <div class="temp-amp">
                <div class="temp temp-min"><img class="temp-icon" src="./assets/icons/cold.png" alt=""> Min: {{ weather.main.temp_min | roundNumber }}°C</div>
                <div class="temp temp-max"><img class="temp-icon" src="./assets/icons/warm.png" alt=""> Max: {{ weather.main.temp_max | roundNumber }}°C</div>
              </div>
            </div>
            <div class="weather-infos">
              <img class="weather-icon" v-bind:src="'https://openweathermap.org/img/wn/' + `${weather.weather[0].icon}` + '@2x.png'" alt="weather icon">
              <div class="weather">{{ weather.weather[0].main }}</div>
              <div class="weather-description">{{ weather.weather[0].description | capitalize}}</div>
            </div>
          </div>
        </div>
        <div class="forecasts-box">
          <h2 class="forecasts-title">Weather Forecasts for Next Days</h2>
          <div class="forecasts-container">
            <div class="forecast" v-for="(item, index) in forecast.list" :key="index">
              <div class="forecast-date">{{ item.dt_txt | formatDate}}</div>
              <div class="forecast-temp">{{ item.main.temp | roundNumber }} °C</div>
              <div class="forecast-weather">{{ item.weather[0].description | capitalize }}</div>
              <img v-bind:src="'https://openweathermap.org/img/wn/' + `${item.weather[0].icon}` + '@2x.png'" alt="forcast weather icon">
            </div>
          </div>
         </div>
      </div>
    </main>
  </div>
</template>

<script>
import moment from 'moment'
export default {
  name: 'App',

  data () {
    return {
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {},
      forecast: {},
    }
  },

  methods: {
    fetchWeather () {
      if (event.key == "Enter") {
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${process.env.VUE_APP_API_KEY}`)
        .then(res => {
          return res.json();
        }).then(this.setResults);
        fetch(`${this.url_base}forecast?q=${this.query}&units=metric&APPID=${process.env.VUE_APP_API_KEY}`)
        .then(foreRes => {
          return foreRes.json();
        }).then(this.setForeResults);
      }
    },

    setResults (results) {
      this.weather = results;
    },

    setForeResults (foreResults) {
      this.forecast = foreResults;
    },

    dateBuilder () {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday", "Sunday"];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },
  },

   filters: {
      upperCase: function (value) {
        value = value.toUpperCase();
        return value
      },
      formatDate: function (value) {
        value = moment(value).format('dddd Do, ha');
        return value
      },
      roundNumber: function (value) {
        value = Math.round(value);
        return value
      },
      capitalize: function (value) {
        value = value.charAt(0).toUpperCase() + value.slice(1)
        return value
      }
    }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Montserrat', sans-serif;
}

#app {
  background-image: url('../src/assets/cold-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

#app.warm {
  background-image: url('../src/assets/warm-bg.png');
}

main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.15), rgba(0, 0, 0, 0.60));
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  color: #313131;
  font-size: 20px;
  appearance: none;
  border: none;
  outline: none;
  background: none;
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 15px 15px 15px 15px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 15px 15px 15px 15px;
}

.current-title {
  color: white;
  text-shadow: 5px rgba(128, 128, 128, 0.418);
  text-align: center;
  font-size: 2.5rem;
  margin-bottom: 50px;
}

.location-box .date {
  color: #ffffff;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align :center;
  margin-bottom: 40px;
}

.location-box .location {
  color: #ffffff;
  font-size: 50px;
  font-weight: 500;
  text-align :center;
}

.weather-box {
  text-align: center;
}

.temp {
  margin-top: 15px;
}

.temp-infos {
  display: inline-block;
  padding: 25px 25px;
  color: #ffffff;
  border-radius: 10px;
  margin: 30px 0px;
}

.temp-main {
  font-size: 80px;
  font-weight: 900;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.15);
}

.temp-amp {
  display: flex;
  justify-content: space-between;
}

.temp-icon {
  display: block;
  max-height: 30px;
  margin: 0 auto;
}

.temp-min {
  color: rgb(132, 220, 255);
}

.temp-max {
  color: rgb(255, 167, 132);
}

.weather-icon {
  display: block;
  height: 80px;
  margin: 0 auto;
}

.weather-box {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  width: 40%;
  margin: 0 auto;
}

.weather-box .weather{
  color: #ffffff;
  font-size: 30px;
  font-weight: 700;
  font-style: italic;
}

.weather-box .weather-description{
  color: #ffffff;
  font-style: italic;
}

.weather-infos {
  margin-bottom: 80px;
}

.forecasts-title {
  color: white;
  font-size: 2.2rem;
  text-align: center;
  margin-bottom: 30px;
}

.forecast {
  width: 100%;
  height: 30vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  color: white;
  font-size: 1.3rem;
}

.forecast-date {
  font-style: italic;
}

@media screen and (min-width: 900px) {
  main {
    height: 100vh;
  }

  .search-box {
    width: 50vw;
    margin: 0 auto;
    margin-bottom: 20px;
  }

  .weather-wrap {
    display: flex;
    justify-content: space-between;
  }

  .temp-infos {
    display: inline-block;
    padding: 25px 25px;
    color: #ffffff;
    border-radius: 10px;
    margin: 0px;
  }

  .weather-box {
    height: 20vh;
  }

  .weather-icon {
    height: 120px;
  }

  .current-weather-box {
    width: 30vw;
    height: 50vh;
  }

  .forecasts-box {
    width: 60vw;
  }

  .forecasts-container {
    display: flex;
    flex-direction: row;
    flex-wrap: wrap;
    justify-content: space-around;
    height: 73vh;
    overflow-y: scroll;
    scrollbar-color: #a8a8a8 rgba(128, 128, 128, 0.137);
    scrollbar-width: thin;
  }

  .forecast {
    width: 30%;
    height: 35%;
    display: flex;
    flex-direction: column;
    font-size: 1rem;
    margin-bottom: 45px;
  }
}

@media screen and (min-width: 1300px) {
  .forecast {
    width: 20%;
    height: 35%;
    margin-bottom: 45px;
  }
}
</style>
