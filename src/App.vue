<template>
  <HeaderComp @search="getWeatherByCityName"/>

  <section>
    <div id="weatherBlock">
      <h3>Погода в вашем городе:</h3>

      <div id="weatherBlockInfo">
        <img :src="mainBgImage" id="weatherBlockInfoBgImage" alt="Задний фон">
        <div id="weatherBlockMainInfo">
          <img :src="imagePath">
          <span>
            <p>{{ mainCityName }}</p>
            <p>Сейчас {{ date.getDate() }} {{ month }}, {{ date.toString().slice(16, 21) }}</p>
            <p>{{ mainTemperature }}&#176;</p>
            <p>Ощущается как {{ realMainTemperature }}&#176;</p>
            <p>  {{ weatherCond }}</p>
          </span> 
        </div>

        <div id="weatherBlockInfo-other">
          <div id="weatherBlockInfoWind" class="weatherB">
            <span>
              <img src="@/assets/free-icon-compass-1163637.png" alt="Направление ветра">
              <p>Направление ветра: {{ weatherRoute }}</p>
            </span>
            <span>
              <img src="@/assets/free-icon-windy-1163672.png" alt="Скорость ветра">
              <p>Скорость ветра {{ weatherSpeed }}м/с</p>
            </span>
          </div>

          <div id="weatherBlockInfoSun" class="weatherB">
            <div>
              <img src="@/assets/free-icon-sunrise-1163663.png" alt="Время рассвета">
              <p>Рассвет в {{ sunriseTime }}</p>
            </div>
            <div>
              <img src="@/assets/free-icon-sunset-1163664.png" alt="Время заката">
              <p>Закат в {{ sunsetTime }}</p>
            </div>
          </div>

          <div id="weatherBlockInfoAir" class="weatherB">
            <span>
              <img src="@/assets/free-icon-humidity-1163648.png" alt="Влажность воздуха">
              <p>Влажность воздуха {{ humidity - 5 }}%</p>
            </span>
            <span>
              <img src="@/assets/free-icon-warm-1163666.png" alt="Атмосферное давление">
              <p>Давление {{ pressure - 275 }}мм рт. ст.</p>
            </span>
          </div>

          <div id="weatherBlockInfoTemp" class="weatherB">
            <span>
              <img src="@/assets/free-icon-warm-1163667.png" alt="Максимальная температура">
              <p>Максимальная температура {{ weatherMax }}&#176;</p>
            </span>
            <span>
              <img src="@/assets/free-icon-cold-1163665.png" alt="Минимальная температура">
              <p>Минимальная температура {{ weatherMin }}&#176;</p>
            </span>
          </div>

        </div>
      </div>
    </div>

    <div id="otherCities">
      <h3>Погода в других городах:</h3>
      <div>
        <ul>
          <li v-for="(item, index) in otherCities" :key="index">
            <img :src="item.imagePath">
            <span>
              <p>{{ item.name }}</p>
              <p>Сейчас {{ item.date.getDate() }} {{ item.month }}, {{ item.date.toString().slice(16, 21) }}</p> 
              <p>{{ item.temperature }}&#176;</p>
              <p>{{ item.weatherCond }}</p>
            </span>
          </li>
        </ul>
      </div>
    </div>
  </section>

  <FooterComp/>
</template>

<script>
import axios from 'axios';

import HeaderComp from './components/HeaderComp.vue';
import FooterComp from './components/FooterComp.vue';

export default {
  name: 'App',
  data(){
    return {
      otherCities: [{
        name: 'Лондон', 
        temperature: '', 
        weatherCond: '', 
        imagePath: '',
        date: 0,
        month: '',
      }, 
      {
        name: 'Нью-Йорк', 
        temperature: '', 
        weatherCond: '', 
        imagePath: '',
        date: 0,
        month: '',
      }, 
      {
        name: 'Токио',
        temperature: '', 
        weatherCond: '', 
        imagePath: '',
        date: 0,
        month: '',
      }, 
      {
        name: 'Будапешт', 
        temperature: '', 
        weatherCond: '', 
        imagePath: '',
        date: 0,
        month: '',
      }],
      months: ['января', 'февраля', 'марта', 'апреля', 'мая', 'июня', 'июля', 'августа', 'сентября', 'октября', 'ноября', 'декабря'],
      mainCityName: 'Москва',
      mainBgImage: '',
      imagePath: '',
      mainTemperature: 0,
      realMainTemperature: 0,
      weatherMax: 0,
      weatherMin: 0,
      weatherCond: '',
      weatherSpeed: 0,
      weatherRoute: '',
      humidity: 0,
      pressure: 0,
      sunrise: 0,
      sunriseTime: 0,
      sunset: 0,
      sunsetTime: 0,
      date: new Date(),
      month: 0,
      timezone: 0,
    }
  },    
  methods: {
    getWeatherByCityName(cityName){
      this.mainCityName = cityName;
      this.getMainWeather();
      this.getDateInfo(this.date);
    },
    getDateInfo(date){

      if (date == this.sunset){

        this.sunsetTime = date.toString().slice(16, 21);

      } else if (date == this.sunrise){ 

        this.sunriseTime = date.toString().slice(16, 21);

      } else {

        this.date = new Date(date.getFullYear(), date.getMonth(), date.getDate(), (date.getHours() + this.timezone), date.getMinutes());
        this.day = this.date.getDate();  
        this.month = this.months[this.date.getMonth()];

        this.otherCities.forEach((item, i) => {
          switch(i) {
            case 0: 
              item.date = new Date(date.getFullYear(), date.getMonth(), date.getDate(), (date.getHours() - 3), date.getMinutes());
              item.month = this.months[item.date.getMonth()];
              break; 
            case 1: 
              item.date = new Date(date.getFullYear(), date.getMonth(), date.getDate(), (date.getHours() - 8), date.getMinutes());
              item.month = this.months[item.date.getMonth()];
              break;
            case 2: 
              item.date = new Date(date.getFullYear(), date.getMonth(), date.getDate(), (date.getHours() + 6), date.getMinutes());
              item.month = this.months[item.date.getMonth()];
              break;
            case 3: 
              item.date = new Date(date.getFullYear(), date.getMonth(), date.getDate(), (date.getHours() - 2), date.getMinutes());
              item.month = this.months[item.date.getMonth()];
              break;
          }
        });
      }
    }, 
    async getAdditionalWeather(){
      for (let item of this.otherCities){

        let geoUrl = new URL(`http://api.openweathermap.org/geo/1.0/direct?q=${item.name}&appid=8a6ebc47b8a6d70277b0d88e2983cdbc`);
        let result = await axios.get(geoUrl);

        let weatherUrl = new URL(`http://api.openweathermap.org/data/2.5/weather?lat=${result.data[0].lat}&lon=${result.data[0].lon}&lang=ru&units=metric&appid=8a6ebc47b8a6d70277b0d88e2983cdbc`);
        let finalResult = await axios.get(weatherUrl);

        switch(finalResult.data.weather[0].description){
          case 'пасмурно': 
            item.imagePath = require('@/assets/free-icon-cloud-1163624.png');
            break;
          case 'переменная облачность': 
            item.imagePath = require('@/assets/free-icon-cloudy-1163634.png');
            break;
          case 'облачно с прояснениями':
          case 'небольшая облачность': 
            item.imagePath = require('@/assets/free-icon-cloudy-1163661.png');
            break;
          case 'ясно': 
            item.imagePath = require('@/assets/free-icon-sun-1163662.png');
            break;
          case 'небольшой дождь': 
          case 'небольшая морось':
          case 'небольшой проливной дождь':
            item.imagePath = require('@/assets/free-icon-cloudy-1163657.png');
            break;
          case 'дождь': 
            item.imagePath = require('@/assets/free-icon-rainy-1163626.png');
            break;
          case 'мгла': 
          case 'туман': 
          case 'дымка':
          case 'плотный туман':
            item.imagePath = require('@/assets/free-icon-foog-1163640.png');
            break;
          case 'снег':
          case 'небольшой снег':
            item.imagePath = require('@/assets/free-icon-snowy-1163629.png');
            break;
        }

        item.temperature = `${Math.round(finalResult.data.main.temp)}`;
        item.weatherCond = finalResult.data.weather[0].description[0].toUpperCase() + finalResult.data.weather[0].description.slice(1);
      }
    },
    async getMainWeather(){

      let geoUrl = new URL(`http://api.openweathermap.org/geo/1.0/direct?q=${this.mainCityName}&appid=8a6ebc47b8a6d70277b0d88e2983cdbc`);
      let result = await axios.get(geoUrl);

      let weatherUrl = new URL(`http://api.openweathermap.org/data/2.5/weather?lat=${result.data[0].lat}&lon=${result.data[0].lon}&lang=ru&units=metric&appid=8a6ebc47b8a6d70277b0d88e2983cdbc`);
      let finalResult = await axios.get(weatherUrl);
      this.timezone = (finalResult.data.timezone / 3600) - 3;
          
      switch(finalResult.data.weather[0].description){
        case 'пасмурно': 
          this.imagePath = require('@/assets/free-icon-cloud-1163624.png');
          this.mainBgImage = require('@/assets/mainlyCloudy.png');
          break;
        case 'переменная облачность': 
          this.imagePath = require('@/assets/free-icon-cloudy-1163634.png');
          this.mainBgImage = require('@/assets/cloudy.png');
          break;
        case 'облачно с прояснениями':
        case 'небольшая облачность':  
          this.imagePath = require('@/assets/free-icon-cloudy-1163661.png');
          this.mainBgImage = require('@/assets/cloudy.png');
          break;
        case 'ясно': 
          this.imagePath = require('@/assets/free-icon-sun-1163662.png');
          this.mainBgImage = require('@/assets/sunny.png');
          break;
        case 'небольшой дождь': 
        case 'небольшая морось':
        case 'небольшой проливной дождь':
          this.imagePath = require('@/assets/free-icon-cloudy-1163657.png');
          this.mainBgImage = require('@/assets/rainy.png');
          break;
        case 'дождь': 
          this.imagePath = require('@/assets/free-icon-rainy-1163626.png');
          this.mainBgImage = require('@/assets/strongRainy.png');
          break;
        case 'мгла': 
        case 'туман':  
        case 'дымка':
        case 'плотный туман':
          this.imagePath = require('@/assets/free-icon-foog-1163640.png');
          this.mainBgImage = require('@/assets/fog.png');
          break;
        case 'снег':
        case 'небольшой снег':
          this.imagePath = require('@/assets/free-icon-snowy-1163629.png');
          this.mainBgImage = require('@/assets/snowfall.png');
          break;
      }

      if (finalResult.data.wind.deg >= 350 && finalResult.data.wind.deg <= 10){

        this.weatherRoute = 'С';

      } else if (finalResult.data.wind.deg > 10 && finalResult.data.wind.deg < 80) {

        this.weatherRoute = 'СВ';

      } else if (finalResult.data.wind.deg >= 80 && finalResult.data.wind.deg <= 100){

        this.weatherRoute = 'В';

      } else if (finalResult.data.wind.deg > 100 && finalResult.data.wind.deg < 170){

        this.weatherRoute = 'ЮВ';

      } else if (finalResult.data.wind.deg >= 170 && finalResult.data.wind.deg <= 190){

        this.weatherRoute = 'Ю';

      } else if (finalResult.data.wind.deg > 190 && finalResult.data.wind.deg < 260){

        this.weatherRoute = 'ЮЗ';

      } else if (finalResult.data.wind.deg >= 260 && finalResult.data.wind.deg <= 280){

        this.weatherRoute = 'З';

      } else if (finalResult.data.wind.deg > 280 && finalResult.data.wind.deg < 350){

        this.weatherRoute = 'СЗ';
      }

      this.sunrise = new Date(finalResult.data.sys.sunrise * 1000);
      this.sunset = new Date(finalResult.data.sys.sunset * 1000);
      this.getDateInfo(this.sunrise);
      this.getDateInfo(this.sunset);

      this.pressure = finalResult.data.main.pressure;
      this.humidity = finalResult.data.main.humidity;

      this.weatherMax = finalResult.data.main.temp_max;
      this.weatherMin = finalResult.data.main.temp_min;

      this.weatherSpeed = Math.round(finalResult.data.wind.speed);

      this.mainTemperature = Math.round(finalResult.data.main.temp);
      this.weatherCond = `${finalResult.data.weather[0].description[0].toUpperCase() + finalResult.data.weather[0].description.slice(1)}`;
      this.realMainTemperature = Math.round(finalResult.data.main.feels_like);
    }
  },
  beforeMount(){

    this.getMainWeather();
    this.getAdditionalWeather();
    this.getDateInfo(new Date());

    setInterval(() => this.getDateInfo( new Date() ), 1000);
    setInterval(() => this.getAdditionalWeather(), 60000);
    setInterval(() => this.getMainWeather(), 60000);
  },
  components: {
    HeaderComp,
    FooterComp
  }
}
</script>

<style lang="scss">
  @import url('https://fonts.googleapis.com/css2?family=Open+Sans:wght@400;600&display=swap');

  // variables 

  $font-size-h3: 26px;
  $font-size-p: 18px;
  $font-size-mainInfo-p: 20px;
  $bg-color: white;

  // styles

  * {
    margin: 0;
    padding: 0;
  }

  section {
    display: flex;
    justify-content: space-between;
    width: 100%;
    min-height: 700px;
    #weatherBlock {
      display: flex;
      justify-content: flex-start;
      align-items: flex-start;
      flex-wrap: wrap;
      margin-top: 50px;
      margin-left: 50px;
      width: 55%;
      min-height: 580px;
      height: auto;
      font-family: 'Mono Sans', sans-serif;
      h3 {
        height: 7.5%;
        font-size: $font-size-h3;
      }
      #weatherBlockInfo {
        position: relative;
        display: flex;
        justify-content: center;
        align-items: flex-start;
        flex-wrap: wrap;
        width: 100%;
        min-height: 90%;
        height: auto;
        #weatherBlockInfoBgImage {
          position: absolute;
          width: 100%;
          height: 100%;
          object-fit: cover;
          border-radius: 10px;
          z-index: -1;
        }
        #weatherBlockMainInfo {
          display: flex;
          justify-content: flex-start;
          align-items: flex-start;
          margin-top: 5%;
          width: 90%;
          height: 30%;
          border-radius: 10px;
          background-color: $bg-color;
          font-size: 22px;
          img {
            margin: 3.5%;
            width: 17.5%;
            height: 70%;
          }
          span {
            display: flex;
            flex-wrap: wrap;
            margin-top: 3%;
            width: 72.5%;
            p:nth-child(1) {
              width: 100%;
              width: 75%;
            } 
            p:nth-child(2) {
              width: 100%;
              margin-top: 15px;
              font-size: $font-size-p;
            }
            p:nth-child(3) {
              margin-top: 20px;
              font-size: 30px;
            }
            p:nth-child(4) {
              margin-top: 30px;
              margin-left: 5px;
              font-size: $font-size-mainInfo-p;
            }
            p:nth-child(5) {
              margin-top: 30px;
              margin-left: 20px;
              font-size: $font-size-mainInfo-p;
            }
          }
        }
        #weatherBlockInfo-other {
          position: relative;
          display: flex;
          justify-content: space-between;
          flex-wrap: wrap;
          width: 90%;
          height: 35vh;
          .weatherB {
            display: flex;
            width: 47.5%;
            height: 45%;
            border-radius: 10px;
            background-color: $bg-color;
          }
          #weatherBlockInfoWind {
            align-items: center;
            flex-wrap: wrap;
            span {
              display: flex;
              align-items: center;
              flex-wrap: wrap;
              margin-left: 20px;
              width: 90%;
              img {
                width: 15%;
                height: 80%;
                background-image: cover;
              }
              p {
                margin-left: 5%;
                width: 75%;
                font-size: $font-size-p;
              }
            }
          }
          #weatherBlockInfoSun {
            div {
              display: flex;
              flex-wrap: wrap;
              justify-content: center;
              align-items: center;
              width: 50%;
              height: 100%;
              img {
                width: 60%;
                background-size: cover;
              }
              p {
                margin-top: -20px;
              }
            }
          }
          #weatherBlockInfoAir {
            align-items: center;
            flex-wrap: wrap;
            height: 47.5%;
            span {
              display: flex;
              align-items: center;
              margin-left: 2.5%;
              width: 90%;
              height: 47.5%;
              img {
                width: 20%;
                height: 80%;
                background-image: cover;
              }
              p {
                font-size: $font-size-p;
              }
            }
          }
          #weatherBlockInfoTemp {
            align-items: center;
            flex-wrap: wrap;
            height: 47.5%;
            span {
              display: flex;
              align-items: center;
              margin-left: 2.5%;
              width: 90%;
              height: 47.5%;
              img {
                width: 20%;
                height: 80%;
                background-image: cover;
              }
              p {
                line-height: 25px;
                font-size: $font-size-p;
              }
            }
          }
        }
      }
    }
    #otherCities {
      display: flex;
      justify-content: flex-start;
      align-items: center;
      flex-wrap: wrap;
      margin-top: 50px;
      margin-right: 50px;
      width: 30%;
      min-height: 600px;
      height: auto;
      font-family: 'Mono Sans', sans-serif;
      h3 {
        height: 7.5%;
        font-size: $font-size-h3;
      }
      div {
        width: 100%;
        height: 89%;
        border-radius: 10px;
        background-color: rgb(0, 0, 134);
        ul {
          list-style-type: none;
          li {
            display: flex;
            justify-content: flex-start;
            align-items: flex-start;
            margin: 0 15px;
            margin-top: 20px;
            height: 115px;
            border-radius: 5px;
            background-color: $bg-color;
            img {
              margin-top: 15px;
              margin-left: 10px;
              min-width: 80px;
              height: 80px;
              background-size: cover;
            }
            span {
              display: flex;
              flex-wrap: wrap;
              margin-top: 15px;
              margin-left: 20px;
              font-size: $font-size-p;
              p:nth-child(2) {
                width: 270px;
                margin-top: 5px;
                font-size: 14px;
              }
              p:nth-child(3) {
                margin-top: 10px;
                font-size: 22px;
              }
              p:nth-child(4) {
                margin-top: 16px;
                margin-left: 10px;
                font-size: 16px;
              }
            }
          }
          li:first-child {
            margin-top: 25px;
          }
        }
      }
    }
  }

  @media (max-width: 480px) {
    section {
      display: flex;
      justify-content: center;
      flex-wrap: wrap;
      width: 100%;
      #weatherBlock {
        width: 90%;
        margin-left: 0px;
        h3 {
          height: 3.5%;
          font-size: 22px;
        }
        #weatherBlockInfo {
          min-height: 910px;
          #weatherBlockMainInfo {
            justify-content: center;
            flex-wrap: wrap;
            height: auto;
            img {
              width: 50%;
              height: 15%;
            }
            span {
              justify-content: center;
              width: 100%;
              p {
                text-align: center;
              }
              p:nth-child(1){
                margin-top: -10px;
                font-size: $font-size-mainInfo-p;
              }
              p:nth-child(2){
                margin-top: 10px;
                font-size: $font-size-mainInfo-p;
              }
              p:nth-child(3){
                margin-top: 10px;
                font-size: 26px;
              }
              p:nth-child(4){
                margin-top: 16px;
                font-size: $font-size-p;
              }
              p:nth-child(5){
                margin-top: 10px;
                margin-bottom: 20px;
                margin-left: 0px;
                width: 100%;
                font-size: $font-size-p;
              }
            }
          }
          #weatherBlockInfo-other {
            height: 50%;  
            .weatherB {
              margin-top: 25px;
              width: 100%;
              height: 150px;
            }
            #weatherBlockInfoAir {
              height: 150px;
            }
            #weatherBlockInfoTemp {
              margin-bottom: 25px;
              height: 150px;
            }
          }  
        }
      }
      #otherCities {
        width: 90%;
        min-height: 680px;
        margin-right: 0px;
        h3 {
          height: 5%;
          font-size: 22px;
        }
        div {
          height: 95%;
          ul {
            li {
              min-height: 135px;
              img {
                min-width: 70px;
              }
              span {
                margin-left: 10px;
                width: 65%;
                p:nth-child(3) {
                  width: 100%;
                }
                p:nth-child(4) {
                  margin-top: 5px;
                  margin-left: 0px;
                  width: 100%;
                  height: auto;
                  word-break: break-word;
                }
              }
            }
          }
        }
      }
    }
  }

  @media (min-width: 1800px) {
    
  }

</style>
