<template>
  <div id="app" :class="typeof weather.main != 'undefined' && weather.main.temp > 16 ? 'warm' :  ''">
    <main>
      <div class="search-box">
        <input type="text" class="search-bar" placeholder="Search a place..." v-model="query" @keypress="fetchWeather">
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="date">{{ dateBuilder() }}</div>
          <div class="location">{{ weather.name }} {{ weather.sys.country }}</div>
        </div>

        <div class="weather-box">
          <div class="temp-infos">
            <div class="temp-main">{{ Math.round(weather.main.temp) }}°C</div>
            <div class="temp temp-felt">Felt: {{ Math.round(weather.main.feels_like) }}°C</div>
            <div class="temp-amp">
              <div class="temp temp-min"><img class="temp-icon" src="./assets/icons/cold.png" alt=""> Min: {{ Math.round(weather.main.temp_min) }}°C</div>
              <div class="temp temp-max"><img class="temp-icon" src="./assets/icons/warm.png" alt=""> Max: {{ Math.round(weather.main.temp_max) }}°C</div>
            </div>
          </div>
          <img class="weather-icon" src="./assets/icons/clouds.png" alt="weather icon">
          <div class="weather">{{ weather.weather[0].main }}</div>
          <div class="weather-description">{{ weather.weather[0].description.charAt(0).toUpperCase() + weather.weather[0].description.slice(1) }}</div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: 'App',

  data () {
    return {
      api_key: 'f743991771b320407a7f62578c7a83fd',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {},
      icons: [
        {
          name: 'Rain',
          src: require('./assets/icons/rain.png')
        },
        {
          name: 'Clear',
          src: require('./assets/icons/sun.png')
        },
        {
          name: 'Snow',
          src: require('./assets/icons/snowy.png')
        },
        {
          name: 'Clouds',
          src: require('./assets/icons/clouds.png')
        },
        {
          name: 'Extreme',
          src: require('./assets/icons/thunder.png')
        },
      ]
    }
  },

  methods: {
    fetchWeather () {
      if (event.key == "Enter") {
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
        .then(res => {
          return res.json();
        }).then(this.setResults);
      }
    },

    setResults (results) {
      this.weather = results;
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

    iconDisplayer () {
      
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
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
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
  background-color: rgba(136, 136, 136, 0.25);
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
  max-height: 60px;
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

</style>
