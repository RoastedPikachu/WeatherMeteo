<template>
  <HeaderComp @search="whatCity"/>
  <section>
    <div id="weatherBlock">
      <h3>Погода в вашем городе:</h3>
      <div id="weatherBlockInfo">
        <div id="weatherBlockMainInfo">
          <img :src="path">
          <span>
            <p>{{ name }}</p>
            <p>Сейчас {{ day }} {{ currentMonth }}, {{ currentTime }}</p>
            <p>{{ weather }}&#176;</p>
            <p>Ощущается как {{ realWeather }}&#176;</p>
            <p>{{ weatherCond }}</p>
          </span> 
        </div>
        <div id="weatherBlockInfo-other">
          <div id="weatherBlockInfoWind" class="weatherB">
            <span>
              <img src="@/assets/free-icon-compass-1163637.png">
              <p>Направление ветра: {{ weatherRoute }}</p>
            </span>
            <span>
              <img src="@/assets/free-icon-windy-1163672.png">
              <p>Скорость ветра {{ weatherSpeed }}км/ч</p>
            </span>
          </div>
          <div id="weatherBlockInfoSun" class="weatherB">
            <div>
              <img src="@/assets/free-icon-sunrise-1163663.png" alt="Рассвет" title="Рассвет">
              <p>Рассвет в {{ sunriseTime }}</p>
            </div>
            <div>
              <img src="@/assets/free-icon-sunset-1163664.png" alt="Закат" title="Закат">
              <p>Закат в {{ sunsetTime }}</p>
            </div>
          </div>
          <div id="weatherBlockInfoAir" class="weatherB">
            <span>
              <img src="@/assets/free-icon-humidity-1163648.png">
              <p>Влажность воздуха {{ humidity }}%</p>
            </span>
            <span>
              <img src="@/assets/free-icon-warm-1163666.png">
              <p>Давление {{ pressure - 250 }}мм рт. ст.</p>
            </span>
          </div>
          <div id="weatherBlockInfoTemp" class="weatherB">
            <span>
              <img src="@/assets/free-icon-warm-1163667.png">
              <p>Максимальная температура {{ weatherMax }}&#176;</p>
            </span>
            <span>
              <img src="@/assets/free-icon-cold-1163665.png">
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
            <img :src="item.path">
            <span>
              <p>{{ item.name }}</p>
              <p>Сейчас {{ day }} {{ currentMonth }}, {{ currentTime }}</p> 
              <p>{{ item.weather }}&#176;</p>
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
import HeaderComp from './components/HeaderComp.vue';
import FooterComp from './components/FooterComp.vue';

export default {
  name: 'App',
  data(){
    return {
      otherCities: [{
        name: 'Лондон', 
        weather: '', 
        weatherCond: '', 
        path: '',
        time: 0,
      }, 
      {
        name: 'Нью-Йорк', 
        weather: '', 
        weatherCond: '', 
        path: '',
        time: 0,
      }, 
      {
        name: 'Токио',
        weather: '', 
        weatherCond: '', 
        path: '',
        time: 0,
      }, 
      {
        name: 'Будапешт', 
        weather: '', 
        weatherCond: '', 
        path: '',
        time: 0,
      }],
      months: ['января', 'февраля', 'марта', 'апреля', 'мая', 'июня', 'июля', 'августа', 'сентября', 'октября', 'ноября', 'декабря'],
      name: 'Москва',
      path: '',
      weather: 0,
      realWeather: 0,
      weatherMax: 0,
      weatherMin: 0,
      weatherCond: '',
      weatherSpeed: 0,
      weatherRoute: '',
      humidity: 0,
      pressure: 0,
      time: 0,
      sunrise: 0,
      sunriseTime: 0,
      sunset: 0,
      sunsetTime: 0,
      day: 0,
      currentMonth: 0,
      currentTime: 0,
    }
  },    
  methods: {
    whatCity(city){
      this.name = city;
      this.getMainWeather();
    },
    dateText: function(date){
      if (date.getMinutes() < 10 && !(date.getHours() < 10)) {

        this.time = `${date.getHours()}:0${date.getMinutes()}`;

      } else if (date.getHours() < 10 && !(date.getMinutes() < 10)) {
  
        this.time = `0${date.getHours()}:${date.getMinutes()}`;

      } else if (date.getMinutes() < 10 && date.getHours() < 10) {

        this.time = `0${date.getHours()}:0${date.getMinutes()}`;

      } else {

        this.time = `${date.getHours()}:${date.getMinutes()}`;
      } 
      if (date == this.sunset){

        this.sunsetTime = this.time;
        
      } else if (date == this.sunrise){

        this.sunriseTime = this.time;

      } else {

        this.day = date.getDate();
        let month = date.getMonth();
        this.currentTime = this.time;

        this.currentMonth = this.months[month];
      }
    }, 
    currentWeather: function(){
      for (let item of this.otherCities){

        let geoUrl = new URL(`http://api.openweathermap.org/geo/1.0/direct?q=${item.name}&appid=8a6ebc47b8a6d70277b0d88e2983cdbc`);

        fetch(geoUrl)
        .then(
          response => {
            return response.json();
          }
        )
        .then(
          result => {
            let lat = result[0].lat;
            let lon = result[0].lon;
            let weatherUrl = new URL(`http://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&lang=ru&units=metric&appid=8a6ebc47b8a6d70277b0d88e2983cdbc`);

            
            fetch(weatherUrl)
            .then(
              response => {
                return response.json();
              }
            )
            .then(
              result => {
                switch(result.weather[0].description){
                  case 'пасмурно': item.path = require('@/assets/free-icon-cloud-1163624.png');
                  break;
                  case 'переменная облачность': item.path = require('@/assets/free-icon-cloudy-1163634.png');
                  break;
                  case 'облачно с прояснениями':
                  case 'небольшая облачность': 
                  item.path = require('@/assets/free-icon-cloudy-1163661.png');
                  break;
                  case 'ясно': item.path = require('@/assets/free-icon-sun-1163662.png');
                  break;
                  case 'небольшой дождь': 
                  case 'небольшая морось':
                  case 'небольшой проливной дождь':
                  item.path = require('@/assets/free-icon-cloudy-1163657.png');
                  break;
                  case 'дождь': item.path = require('@/assets/free-icon-rainy-1163626.png');
                  break;
                  case 'мгла': 
                  case 'туман': 
                  case 'дымка':
                  case 'плотный туман':
                  item.path = require('@/assets/free-icon-foog-1163640.png');
                  break;
                }
                item.weather = `${Math.round(result.main.temp)}`;
                item.weatherCond = result.weather[0].description[0].toUpperCase() + result.weather[0].description.slice(1);
              }
            )
            .catch(
              e => {
                console.log(`Произошла ошибка ${e.message}`);
              }
            )
          }
        );
      }
    },
    getMainWeather: function(){

      let geoUrl = new URL(`http://api.openweathermap.org/geo/1.0/direct?q=${this.name}&appid=8a6ebc47b8a6d70277b0d88e2983cdbc`);

      fetch(geoUrl)
      .then(
        response => {
          return response.json();
        }
      )
      .then(
        result => {
      
          let lat = result[0].lat;
          let lon = result[0].lon;
          let weatherUrl = new URL(`http://api.openweathermap.org/data/2.5/weather?lat=${lat}&lon=${lon}&lang=ru&units=metric&appid=8a6ebc47b8a6d70277b0d88e2983cdbc`);

          fetch(weatherUrl)
          .then(
            response => {
              return response.json();
            }
          )
          .then(
            result => {
              switch(result.weather[0].description){
                case 'пасмурно': this.path = require('@/assets/free-icon-cloud-1163624.png');
                break;
                case 'переменная облачность': this.path = require('@/assets/free-icon-cloudy-1163634.png');
                break;
                case 'облачно с прояснениями':
                case 'небольшая облачность': 
                this.path = require('@/assets/free-icon-cloudy-1163661.png');
                break;
                case 'ясно': this.path = require('@/assets/free-icon-sun-1163662.png');
                break;
                case 'небольшой дождь': 
                case 'небольшая морось':
                case 'небольшой проливной дождь':
                this.path = require('@/assets/free-icon-cloudy-1163657.png');
                break;
                case 'дождь': this.path = require('@/assets/free-icon-rainy-1163626.png');
                break;
                case 'мгла': 
                case 'туман': 
                case 'дымка':
                case 'плотный туман':
                this.path = require('@/assets/free-icon-foog-1163640.png');
                break;
              }
              if (result.wind.deg >= 350 && result.wind.deg <= 10){

                this.weatherRoute = 'С';

              } else if (result.wind.deg > 10 && result.wind.deg < 80) {

                this.weatherRoute = 'СВ';

              } else if (result.wind.deg >= 80 && result.wind.deg <= 100){

                this.weatherRoute = 'В';

              } else if (result.wind.deg > 100 && result.wind.deg < 170){

                this.weatherRoute = 'ЮВ';

              } else if (result.wind.deg >= 170 && result.wind.deg <= 190){

                this.weatherRoute = 'Ю';

              } else if (result.wind.deg > 190 && result.wind.deg < 260){

                this.weatherRoute = 'ЮЗ';

              } else if (result.wind.deg >= 260 && result.wind.deg <= 280){

                this.weatherRoute = 'З';

              } else if (result.wind.deg > 280 && result.wind.deg < 350){

                this.weatherRoute = 'СЗ';
              }

              this.sunrise = new Date(result.sys.sunrise * 1000);
              this.sunset = new Date(result.sys.sunset * 1000);
              this.dateText(this.sunrise);
              this.dateText(this.sunset);

              this.pressure = result.main.pressure;
              this.humidity = result.main.humidity;

              this.weatherMax = result.main.temp_max;
              this.weatherMin = result.main.temp_min;

              this.weatherSpeed = Math.round(result.wind.speed);

              this.weather = Math.round(result.main.temp);
              this.weatherCond = `  ${result.weather[0].description[0].toUpperCase() + result.weather[0].description.slice(1)}`;
              this.realWeather = Math.round(result.main.feels_like);
            }
          )
        }
      );
    },
  },
  beforeMount(){

    this.getMainWeather();
    this.currentWeather();
    this.dateText(new Date());
    setInterval(() => this.dateText(new Date()), 1000);
    setInterval(() => this.currentWeather(), 60000);
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
        color: black;
        font-size: 26px;
      }
      #weatherBlockInfo {
        display: flex;
        justify-content: center;
        align-items: flex-start;
        flex-wrap: wrap;
        width: 100%;
        min-height: 90%;
        height: auto;
        border-radius: 10px;
        background-image: url('assets/oblachnost.png');
        background-size: cover;
        background-repeat: no-repeat;
        #weatherBlockMainInfo {
          display: flex;
          justify-content: flex-start;
          align-items: flex-start;
          margin-top: 5%;
          width: 90%;
          height: 30%;
          border-radius: 10px;
          background-color: white;
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
              font-size: 18px;
            }
            p:nth-child(3) {
              margin-top: 20px;
              font-size: 30px;
            }
            p:nth-child(4) {
              margin-top: 30px;
              margin-left: 5px;
              font-size: 20px;
            }
            p:nth-child(5) {
              margin-top: 30px;
              margin-left: 20px;
              font-size: 20px;
            }
          }
        }
        #weatherBlockInfo-other {
          position: relative;
          display: flex;
          justify-content: space-between;
          flex-wrap: wrap;
          width: 90%;
          height: 30vh;
          .weatherB {
            display: flex;
            width: 47.5%;
            height: 45%;
            border-radius: 10px;
            background-color: white;
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
                font-size: 18px;
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
            height: 50%;
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
                font-size: 18px;
              }
            }
          }
          #weatherBlockInfoTemp {
            align-items: center;
            flex-wrap: wrap;
            height: 50%;
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
                font-size: 18px;
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
        color: black;
        font-size: 26px;
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
            background-color: white;
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
              font-size: 18px;
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

  @media (max-width: 450px) {
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
                font-size: 20px;
              }
              p:nth-child(2){
                margin-top: 10px;
                font-size: 20px;
              }
              p:nth-child(3){
                margin-top: 10px;
                font-size: 26px;
              }
              p:nth-child(4){
                margin-top: 16px;
                font-size: 18px;
              }
              p:nth-child(5){
                margin-top: 10px;
                margin-bottom: 20px;
                margin-left: 0px;
                width: 100%;
                font-size: 18px;
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
        min-height: 780px;
        margin-right: 0px;
        h3 {
          height: 5%;
          font-size: 22px;
        }
        div {
          height: 95%;
          ul {
            li {
              height: 160px;
              img {
                min-width: 70px;
              }
              span {
                margin-left: 10px;
                width: 60%;
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

</style>
