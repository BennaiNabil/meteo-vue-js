<template>
  <div id="app" :class="typeof meteo.main != 'undefined' && meteo.main.temp > 20 ? 'chaud' : ''">
    <main>
      <div class="search-box">
        <input v-model="query"
               class="search-bar"
               placeholder="Saisir la ville..."
               type="text"
               @keypress="getMeteoData">
        <button class="minimal"
                @click="getMeteoDataAtUserLocation">
          <i aria-hidden="true" class="fa fa-map-marker icon-4x"></i>
        </button>
      </div>
      <div v-if="typeof meteo.main != 'undefined'" class="weather-wrap">
        <div class="location-box">
          <div class="location"><i class="fas fa-globe-africa"></i>{{ meteo.name }}, {{ meteo.sys.country }}</div>
          <div class="weather">
            {{ meteo.weather[0].description.charAt(0).toUpperCase() + meteo.weather[0].description.slice(1) }}
          </div>
          <div class="date"><i class="fas fa-calendar-alt"></i>{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box flex flex-wrap -mx-1 overflow-hidden sm:-mx-1 md:-mx-2 lg:-mx-3 xl:-mx-2">
          <div
              class="my-1 px-1 w-full overflow-hidden sm:my-1 sm:px-1 md:my-2 md:px-2 md:w-1/2 lg:my-3 lg:px-3 lg:w-1/4 xl:my-2 xl:px-2 xl:w-1/4">
            <div class="temp">{{ Math.round(meteo.main.temp) }}°C</div>
          </div>

          <div
              class="my-1 px-1 w-full overflow-hidden sm:my-1 sm:px-1 md:my-2 md:px-2 md:w-1/2 lg:my-3 lg:px-3 lg:w-1/4 xl:my-2 xl:px-2 xl:w-1/4">
            <div class="wind"><i class="fas fa-wind"></i>{{ Math.round(3.6 * meteo.wind.speed) }} km/h</div>

          </div>

          <div
              class="my-1 px-1 w-full overflow-hidden sm:my-1 sm:px-1 md:my-2 md:px-2 md:w-1/2 lg:my-3 lg:px-3 lg:w-1/4 xl:my-2 xl:px-2 xl:w-1/4">
            <div class="sunrise"><i class="fas fa-sun"></i>{{ this.timeConverter(meteo.sys.sunrise) }}</div>

          </div>

          <div
              class="my-1 px-1 w-full overflow-hidden sm:my-1 sm:px-1 md:my-2 md:px-2 md:w-1/2 lg:my-3 lg:px-3 lg:w-1/4 xl:my-2 xl:px-2 xl:w-1/4">
            <div class="sunset"><i class="fas fa-moon"></i>{{ this.timeConverter(meteo.sys.sunset) }}</div>

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
    getMeteoDataAtUserLocation() {
      navigator.geolocation.getCurrentPosition(
          position => {
            fetch(`${this.url_base}weather?lat=${position.coords.latitude}&lon=${position.coords.longitude}&units=metric&APPID=${this.api_key}&lang=fr`)
                .then(response => {
                  return response.json();
                })
                .then(this.setResults);
            console.log(`Longitude : ${position.coords.latitude}`);
            console.log(`Latitude : ${position.coords.longitude}`);
          },
          positionError => {
            console.log(positionError.message);
          }
      );
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
    },
    doubleDigit(d) {
      return d < 9 ? '0' + d : d;
    },
    timeConverter(UNIX_timestamp) {
      const a = new Date(UNIX_timestamp * 1000);
      const hour = this.doubleDigit(a.getHours());
      const min = this.doubleDigit(a.getMinutes());
      return `${hour}:${min}`;
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
  font-family: 'montserrat', sans-serif;
}

#app {
  background: #2980B9; /* fallback for old browsers */
  background: -webkit-radial-gradient(#FFFFFF, #6DD5FA, #2980B9); /* Chrome 10-25, Safari 5.1-6 */
  background: radial-gradient(#FFFFFF, #6DD5FA, #2980B9); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
}

#app.chaud {
  background: #F00; /* fallback for old browsers */
  background: -webkit-radial-gradient(#FF6A00, #EE0979, #F00); /* Chrome 10-25, Safari 5.1-6 */
  background: radial-gradient(#FF6A00, #EE0979, #F00); /* W3C, IE 10+/ Edge, Firefox 16+, Chrome 26+, Opera 12+, Safari 7+ */
}

main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, .25), rgba(0, 0, 0, .75));
}

.search-box {
  width: 100%;
  margin-bottom: 100px;
  display: inline-flex;
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

.weather {
  color: #FFF;
  font-size: 32px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  text-align: center;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
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

.weather-box .sunrise {
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

.weather-box .sunset {
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

button.minimal {
  background-color: rgba(255, 255, 255, 0.25);
  font: bold 12px/1 "helvetica neue", helvetica, arial, sans-serif;
  padding: 8px 0 9px;
  text-align: center;
  text-shadow: 0 1px 0 #fff;
  width: 150px;
  margin: 0 10px;
}

@media all and (max-width: 768px) {
    .weather-box .temp,
    .weather-box .wind,
    .weather-box .sunrise,
    .weather-box .sunset
    {
      font-size: 35px;
    }
}

</style>
