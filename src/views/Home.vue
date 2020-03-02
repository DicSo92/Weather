<template>
  <ion-app>
    <ion-split-pane when="sm">
      <ion-menu>
        <ion-header>
          <ion-toolbar color="primary">
            <ion-title>Menu</ion-title>
          </ion-toolbar>
        </ion-header>

        <ion-content>
          <ion-list>
            <ion-list-header>
              Navigate
            </ion-list-header>
            <ion-menu-toggle auto-hide="false">
              <ion-item button>
                <ion-icon slot="start" name='home'></ion-icon>
                <ion-label>
                  Home
                </ion-label>
              </ion-item>
            </ion-menu-toggle>
          </ion-list>
        </ion-content>
      </ion-menu>

      <div class="ion-page" main>
        <ion-header>
          <ion-toolbar>
            <ion-buttons slot="start">
              <ion-menu-toggle>
                <ion-button>
                  <ion-icon slot="icon-only" name="menu"></ion-icon>
                </ion-button>
              </ion-menu-toggle>
            </ion-buttons>
            <ion-title>{{!this.search ? 'Paris' : this.search}}</ion-title>
          </ion-toolbar>
        </ion-header>
        <ion-content class="ion-padding">

          <ion-item>
            <ion-label position="floating">Rechercher une ville...</ion-label>
            <ion-input :value="search" @input="search = $event.target.value" v-on:keyup.enter="getWeather"></ion-input>
          </ion-item>

          <ion-grid>
            <ion-row>
              <ion-col></ion-col>
              <ion-col>
                <div>
                  <ion-icon name="cloudy"></ion-icon>
                </div>
              </ion-col>
              <ion-col>
                <div>
                  {{currentTemp}} °C
                </div>
              </ion-col>
              <ion-col></ion-col>
            </ion-row>
          </ion-grid>

          <ion-list>
            <ion-item>
              <ion-label>Température : Min {{minTemp}} °C <br> Max {{maxTemp}} °C</ion-label>
            </ion-item>
            <ion-item>
              <ion-label>Lever du soleil : {{sunrise}}</ion-label>
            </ion-item>
            <ion-item>
              <ion-label>Coucher du soleil : {{sunset}}</ion-label>
            </ion-item>
            <ion-item>
              <ion-label>Humidité : {{humidity}}</ion-label>
            </ion-item>
            <ion-item>
              <ion-label>Pression atmosphérique : {{pressure}}</ion-label>
            </ion-item>
            <ion-item>
              <ion-label>Vent : {{wind}}</ion-label>
            </ion-item>
          </ion-list>
        </ion-content>
      </div>
    </ion-split-pane>
  </ion-app>
</template>

<script>
  import axios from 'axios'
  export default {
    name: "home",
    data () {
      return {
        search: null,

        currentTemp: '',
        minTemp: '',
        maxTemp:'',
        sunrise: '',
        sunset: '',
        pressure: '',
        humidity: '',
        wind: '',
        overcast: '',
        icon: ''
      }
    },
    methods: {
      getWeather () {
        let rqst = !this.search ? 'paris' : this.search
        let url = 'http://api.openweathermap.org/data/2.5/weather?q=' + rqst + '&units=metric&APPID=' + process.env.VUE_APP_OPEN_WEATHER;
        axios.get(url)
            .then(response => {
              console.log(response.data);
              this.currentTemp = response.data.main.temp;
              this.minTemp = response.data.main.temp_min;
              this.maxTemp = response.data.main.temp_max;
              this.pressure = response.data.main.pressure;
              this.humidity = response.data.main.humidity + '%';
              this.wind = response.data.wind.speed + 'm/s';
              this.overcast = response.data.weather[0].description;
              this.icon = "images/" + response.data.weather[0].icon.slice(0, 2) + ".svg";
              this.sunrise = new Date(response.data.sys.sunrise*1000).toLocaleTimeString("en-GB").slice(0,4);
              this.sunset = new Date(response.data.sys.sunset*1000).toLocaleTimeString("en-GB").slice(0,4);

            })
            .catch(error => {
              console.log(error);
            });

      }
    },
    beforeMount() {
      this.getWeather('paris');
    },
  };
</script>
