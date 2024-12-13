<template>
  <body>
    <div class="container main">
      <h1>Приложение погоды</h1>
      <p v-if="!weather_data">Загрузка ...</p>
      <div v-else>
        <p class="temp">
          Температура в {{ weather_data.name }}: {{ temp }} 
        </p>
        <div class="weather_desc">
          <p class="temp temp__desc">
            {{ weather_description }} 
          </p>
          <img :src="require(`@/assets/images/${weather_img}`)" alt="" v-if="weather_img">
        </div>
        <p class="temp">
          Ощущается как: {{ feels_like }}
        </p>
        <p class="temp">
          Скорость ветра: {{ weather_data.wind.speed }}
        </p>
        <p class="temp">
          Давление: {{ weather_data.main.pressure }}
        </p>
        <p class="temp">
          Уровень влажности: {{ weather_data.main.sea_level }}
        </p>
      </div>
      <h1>Выберите город</h1>
      <select class="select" name="" id="" @change="city($event)">
        <option class="option" value="57.9194,59.965">Нижний Тагил</option>
        <option class="option" value="55.7522,37.6156">Москва</option>
        <option class="option" value="48.7194,44.5018">Волгоград</option>  
        <option class="option" value="56.8519,60.6122">Екатеринбург</option>
      </select>
    </div>
    <div class="sitch-temp">
      <div class="celsius">
        <input @click="switch_temp" class="inpt__radio" type="radio" name="temp" value="celsius" id="celsius" checked>
        <label for="celsius">Цельсия</label>
      </div>
      <div class="fahrenheit">
        <input @click="switch_temp" class="inpt__radio" type="radio" name="temp" value="fahrenheit" id="fahrenheit" >
        <label for="fahrenheit">Фаренгейт</label>
      </div>
    </div>
    
  </body>
  <button class="button" @click="getTwoWeekForeCast(lat_lon)">Получить погоду</button>
  <div class="forecast-container container"> 
    <div v-for="(cardsArray, keyDate) in transaformResponse" :key="keyDate"> 
      <h1 class="time">{{ keyDate }}</h1> 
      <div class="forecast"> 
        <ForecastCard v-for="(item, idx) in cardsArray" :tempetature="item.tempetature" :description="item.description" :time="item.time" :icon="item.icon" :key="idx"></ForecastCard> 
      </div> 
    </div> 
  </div> 
</template>

<script>
import ForecastCard from './components/ForecastCard.vue';


export default {
  name: 'App',


  data(){
    return {
      lat_lon: "57.9194,59.965",
      weather_data: null,
      weather_id: null,
      weather_img: null,
      temp_value: null,
      temp: null,
      feels_like: null,
      forecast_data:null,
      fetchError:null,
      weatherForecastData:null,
      weather_desc: {
        200: "thunderstorm.png", 
        201: "thunderstorm.png", 
        202: "thunderstorm.png", 
        210: "thunderstorm.png", 
        211: "thunderstorm.png", 
        212: "thunderstorm.png", 
        221: "thunderstorm.png", 
        230: "thunderstorm.png", 
        231: "thunderstorm.png", 
        232: "thunderstorm.png",
        300: "drizzle.png",	
        301: "drizzle.png",	
        302: "drizzle.png",	
        310: "drizzle.png",	
        311: "drizzle.png",	
        312: "drizzle.png",	
        313: "drizzle.png",	
        314: "drizzle.png",	 
        321: "drizzle.png",	
        500: "rain.png",	
        501: "rain.png",	
        502: "rain.png",	
        503: "rain.png",	
        504: "rain.png",	
        511: "freezing-rain.png",	
        520: "heavy-rain.png",	
        521: "heavy-rain.png",	
        522: "heavy-rain.png",	
        531: "heavy-rain.png",	
        600: "snow.png",
        601: "snow.png",
        602: "snow.png",
        611: "snow.png",
        612: "snow.png",
        613: "snow.png",
        615: "snow.png",
        616: "snow.png",
        620: "snow.png",
        621: "snow.png",
        622: "snow.png",
        701: "mist.png",	
        711: "mist.png",	
        721: "mist.png",	
        731: "mist.png",	
        741: "mist.png",	
        751: "mist.png",	
        761: "mist.png",	
        762: "mist.png",	
        771: "mist.png",	
        781: "mist.png",	
        800: "clear-sky.png",
        801: "clouds.png",
        802: "clouds.png",
        803: "clouds.png",
        804: "clouds.png"
      }
    }
  },


  mounted(){
    fetch(`https://api.openweathermap.org/data/2.5/weather?lat=57.9194&lon=59.965&appid=514682b9f2f21da6f8b153944b16896d&units=metric&lang=ru`)
    .then(resp=>resp.json())
    .then(json=>{
      this.weather_data=json;
      this.upper();
      this.icon();
      this.switch_temp();
    });

    fetch(`forecast.json`)
    .then(resp=>resp.json())
    .then(json=>{
      this.forecast_data=json;
    });
    
  },

  computed : { 
    transaformResponse (){ 
      console.log(1)
      const data = {} 
      this.weatherForecastData?.list.map((item)=>{ 
        console.log(item) 
        const date = item.dt_txt.split(' ')[0] 
 
        if (!data[date]){ 
          data[date] = [{ 
            dt: item.dt, 
            time: item.dt_txt.split(' ')[1], 
            description: item.weather[0].description, 
            tempetature: item.main.temp, 
            icon: item.weather[0].icon, 
          }] 
        }else{ 
          data[date].push( { 
            dt: item.dt, 
            time: item.dt_txt.split(' ')[1], 
            description: item.weather[0].description, 
            tempetature: item.main.temp, 
            icon: item.weather[0].icon, 
          }) 
        } 
      }) 
      return data 
    }, 
  },

  methods: {
    city(arg){
    this.lat_lon = arg.target.value;
    fetch(`https://api.openweathermap.org/data/2.5/weather?lat=${arg.target.value.split(",")[0]}&lon=${arg.target.value.split(",")[1]}&appid=514682b9f2f21da6f8b153944b16896d&units=metric&lang=ru`)
    .then(resp=>resp.json())
    .then(json=>{
      this.weather_data=json;
    });
    },
    upper(){
      this.weather_description = this.weather_data.weather[0].description[0].toUpperCase() + this.weather_data.weather[0].description.slice(1)
    },
    icon(){
      this.weather_id = this.weather_data.weather[0].id
      this.weather_img = this.weather_desc[this.weather_id]
    },
    switch_temp(){
      this.temp_value = document.querySelector('input[name="temp"]:checked').value;
      if(this.temp_value == "celsius"){
        this.temp = this.weather_data.main.temp;
        this.feels_like = this.weather_data.main.feels_like;
      }
      else if(this.temp_value == "fahrenheit"){
        this.temp = ((Number(this.weather_data.main.temp) * 9/5)+ 32).toFixed(2);
        this.feels_like = ((Number(this.weather_data.main.feels_like) * 9/5)+ 32).toFixed(2);
      }
    },
    getTwoWeekForeCast(lat_lon) {
      if (typeof lat_lon !== 'string') {
        console.error("lat_lon должен быть строкой, получено:", lat_lon);
        return;
      }
      const [lat, lon] = lat_lon.split(",");
      fetch(`http://api.openweathermap.org/data/2.5/forecast?lat=${lat}&lon=${lon}&appid=514682b9f2f21da6f8b153944b16896d&units=metric&lang=ru`)
        .then(data => data.json())
        .then(data => {
          this.weatherForecastData = data;
        })
        .catch(error => {
          this.fetchError = error;
          console.error(error);
      });
    }
  },
  
  components: {
    ForecastCard
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #1a1a1a;
  margin-top: 60px;
}

body {
  background-color: #1a1a1a;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;

}

p {
  color:white;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
  padding: 0px 15px;
  
}

.main {
  border: 3px solid white;
  padding: 10px;
  border-radius: 15px;
  padding-bottom: 20px;
  width: 500px;
  margin: 0;
}

.temp {
  font-size: 24px;
}

.select {
  font-size: 24px;
  background-color: rgb(84, 203, 84);
  color: white;
  border: 3px solid white;
  border-radius: 10px;
  outline: none;
}

.option {
  background-color: #1a1a1a;
}

.weather_desc {
  display: flex;
  text-align: center;
  justify-content: center;
  align-items: center;
}

.temp__desc {
  margin: 0;
  text-align: center;
}

.sitch-temp {
  border: 3px solid white;
  height: 100%;
  padding: 10px;
  border-radius: 10px;
  font-size: 20px;
  margin-left: 20px;
}

.inpt__radio {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  border-radius: 50%;
  width: 16px;
  height: 16px;

  border: 2px solid #999;
  transition: 0.2s all linear;
  outline: none;
  margin-right: 5px;

  position: relative;
  top: 4px;
}

.inpt__radio:checked {
  border: 6px solid white;
}

.time {
  color: white;
}

.forecast {
  display: flex;
  flex-direction: row;
}

.button {
  margin-top: 20px;
  background-color: #1a1a1a;
  border: 3px solid white;
  border-radius: 5px;
  color: white;
  font-size: 2rem;
  cursor: pointer;
}

.button:hover {
  background-color: rgb(84, 203, 84);
}

</style>
