<template>
  <div id="app" :class="typeof meteo.main != 'undefined' && meteo.main.temp > 20 ? 'chaud' : ''">
    <main>
      <div class="search-box">
        <input v-model="query"
               class="search-bar"
               placeholder="Saisir la ville..."
               type="text"
               @keypress="getMeteoData">
      </div>
      <div v-if="typeof meteo.main != 'undefined'" class="weather-wrap">
        <div class="location-box">
          <div class="location"><i class="fas fa-globe-africa"></i>{{ meteo.name }}, {{ meteo.sys.country }}</div>
          <div class="date"><i class="fas fa-calendar-alt"></i>{{ dateBuilder() }}</div>
        </div>
        <div class="weather-box">
          <div class="temp"><i class="fas fa-thermometer-half"></i>{{ Math.round(meteo.main.temp) }}°C</div>
          <div class="wind"><i class="fas fa-wind"></i>{{ Math.round(3.6 * meteo.wind.speed) }} km/h</div>
          <div class="weather">
            {{ meteo.weather[0].description.charAt(0).toUpperCase() + meteo.weather[0].description.slice(1) }}
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>

export default {
  name: 'app',
  data() {
    return {
      api_key: '2608a51c86c1966b353d77621fc34904',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      meteo: {},
    }
  },
  methods: {
    getMeteoData(event) {
      if (event.key === "Enter") {
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}&lang=fr`)
            .then(response => {
              return response.json();
            })
            .then(this.setResults);
      }
    },
    setResults(results) {
      this.meteo = results;
    },
    dateBuilder() {
      let d = new Date();
      let listeMois = ['Janvier', 'Février', 'Mars', 'Avril', 'Mai', 'Juin', 'Juillet', 'Août', 'Septembre', 'Octobre', 'Novembre', 'Décembre'];
      let listeJours = ['Dimanche', 'Lundi', 'Mardi', 'Mercredi', 'Jeudi', 'Vendredi', 'Samedi'];
      let jour = listeJours[d.getDay()];
      let date = d.getDate();
      let mois = listeMois[d.getMonth()];
      let annee = d.getFullYear();
      return `${jour} ${date} ${mois} ${annee}`;
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
  font-family: 'Brush Script MT', cursive;
}
#app {
  background-image: url('./assets/froid-bg.jpg');
  background-size: cover;
  background-position: top;
  transition: 0.4s;
}
#app.chaud {
  background-image: url('./assets/chaud-bg.jpg');
}

main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, .25), rgba(0, 0, 0, .75));
}

.search-box {
  width: 100%;
  margin-bottom: 100px;
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
  background: rgba(255, 255, 255, 0.5) none;
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.25);
  border-radius: 0 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0 0 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0;
}

.location-box .location {
  color: white;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: white;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
  margin: 20px 0;
}

.weather-wrap {
  margin: auto 0;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
  /*display: inline-block;*/
  padding: 10px 25px;
  color: #FFF;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: #FFF;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .wind {
  padding: 10px 25px;
  color: #FFF;
  font-size: 25px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.fas {
  margin: 0 20px;
}

.hot {
  background-color: orange;
}

.cold {
  background-color: deepskyblue;
}
</style>
