<script setup>
    import Search from './components/Search.vue';
    import MainInfo from './components/MainInfo.vue';
    import HourCard from './components/HourCard.vue';
    import { ref } from 'vue'
    import axios from 'axios' 
    import DayCard from './components/DayCard.vue';
    const weatherApiToOwmIcons = {
    1000: '01d',   // Ясно / Clear sky
    1003: '02d',   // Маленькие облака / Few clouds
    1006: '03d',   // Частичная облачность / Scattered clouds
    1009: '04d',   // Пасмурно / Broken clouds
    1030: '50d',   // Туман / Mist
    1063: '10d',   // Легкий дождь / Light rain
    1066: '13d',   // Снежная крупа / Snow shower
    1069: '13d',   // Мокрый снег / Rain and snow mixed
    1072: '10d',   // Лёгкая морось / Drizzle
    1087: '11d',   // Гроза / Thunderstorm
    1114: '13d',   // Метель / Blizzard
    1117: '13d',   // Шквалы снега / Heavy snowfall
    1135: '50d',   // Плотный туман / Foggy
    1147: '50d',   // Дымка / Haze
    1150: '09d',   // Мелкий дождь / Light drizzle
    1153: '09d',   // Моросящий дождь / Light intensity drizzle
    1168: '50d',   // Тонкий ледяной туман / Ice fog
    1171: '50d',   // Пыльная дымка / Smoke haze
    1180: '10d',   // Умеренный дождь / Moderate rain
    1183: '10d',   // Умеренно сильный дождь / Heavy intensity rain
    1186: '10d',   // Дождевые капли / Rain showers
    1189: '10d',   // Ливень / Very heavy rain
    1192: '10n',   // Ночной умеренный дождь / Night moderate rain
    1195: '09n',   // Ночная гроза / Night thunderstorm
    1198: '09d',   // Прерывистый дождь / Intermittent rain
    1201: '09d',   // Интенсивный ливневый дождь / Extreme rain
    1204: '13d',   // Летучий снежный заряд / Shower snow
    1207: '13d',   // Короткий легкий снегопад / Light snow shower
    1210: '13d',   // Снежный поток / Snow flurry
    1213: '13d',   // Сильный кратковременный снегопад / Heavy snow shower
    1216: '13d',   // Неустойчивый слабый снегопад / Light snowfall
    1219: '13d',   // Продолжительный средний снегопад / Continuous light snow
    1222: '13d',   // Очень сильный снегопад / Heavy snowfall
    1225: '13d',   // Большие снежинки / Large snowflakes
    1237: '13d',   // Заморозки / Freezing conditions
    1240: '10d',   // Замерзший дождь / Freezing rain
    1243: '10d',   // Очень интенсивный замерзший дождь / Severe freezing rain
    1246: '10d',   // Умеренные заморозки / Moderate freezing rain
    1249: '13d',   // Льдистая поверхность дороги / Road ice glazing
    1252: '13d',   // Изморозь / Rime
    1255: '13d',   // Ураганные порывы ветра со снегом / Blowing snow
    1258: '13d',   // Бурное усиление ветров со снегом / Wind-driven snow
    1261: '13d',   // Мягкое падение снега / Soft falling snow
    1264: '13d',   // Сухой рыхлый снег / Dry loose snow
    };
    const items = ref({})
    const hour_items = ref({})
    const day_items = ref({})
    const search1 = ref('')
    const error = ref(null)
    const loading = ref(true)
   
    const handleSearch = async (coords) => {
  try {
    let weatherUrl, forecastUrl, dayForecastUrl;
    
    if (coords) {
      // Запросы по координатам
      weatherUrl = `https://api.openweathermap.org/data/2.5/weather?lat=${coords.lat}&lon=${coords.lon}&appid=67c42fdd3811877ec78f29537225fd87&units=metric&lang=ru`;
      forecastUrl = `https://api.openweathermap.org/data/2.5/forecast?lat=${coords.lat}&lon=${coords.lon}&appid=67c42fdd3811877ec78f29537225fd87&units=metric`;
      // Для WeatherAPI используем координаты
      dayForecastUrl = `http://api.weatherapi.com/v1/forecast.json?key=35a449111132472d94090056251407&q=${coords.lat},${coords.lon}&days=7&aqi=no&alerts=no`;
    } else {
      // Запросы по названию города
      weatherUrl = `https://api.openweathermap.org/data/2.5/weather?q=${search1.value}&appid=67c42fdd3811877ec78f29537225fd87&units=metric&lang=ru`;
      forecastUrl = `https://api.openweathermap.org/data/2.5/forecast?q=${search1.value}&appid=67c42fdd3811877ec78f29537225fd87&units=metric`;
      // Для WeatherAPI используем название города
      dayForecastUrl = `http://api.weatherapi.com/v1/forecast.json?key=35a449111132472d94090056251407&q=${search1.value}&days=7&aqi=no&alerts=no`;
    }

    // Делаем все запросы параллельно
    const [weatherRes, forecastRes, dayForecastRes] = await Promise.all([
      axios.get(weatherUrl),
      axios.get(forecastUrl),
      axios.get(dayForecastUrl)
    ]);

    items.value = weatherRes.data;
    hour_items.value = forecastRes.data;
    
    // Обрабатываем данные для прогноза по дням
    day_items.value = dayForecastRes.data.forecast.forecastday.map(day => ({
      date: day.date,
      maxtemp_c: day.day.maxtemp_c,
      mintemp_c: day.day.mintemp_c,
      icon: day.day.condition.code
    }));

  } catch (err) {
    error.value = err.message;
    console.error('Ошибка при загрузке данных:', err);
  } finally {
    loading.value = false;
  }
};

    function sbros(event){
        console.log('Сброс данных!')
        items.value = {}
    }

</script>

<template>
    
  <body>
    
    
    <div v-if="items.main">
        <header class="header">
        <div class="header__logo">
            <img class="header__logo-img" @click="sbros" src="./images/logo (4).png" alt="лого">
        </div>
        <div class="header__title">
            <h1 class="title">BestWeather</h1>
        </div>
    </header>
    <Search 
      :search1="search1" 
      @update:search1="val => search1 = val"
      @search="handleSearch"
    />
        <MainInfo :items="items"/>
        <div v-if="hour_items.list">
            <HourCard :hour_items="hour_items"/></div>
            
        </div>
    <div v-else class="container">
        <header class="header">
        <div class="header__logo">
            <img class="header__logo-img" @click="sbros" src="./images/logo (4).png" alt="лого">
        </div>
        <div class="header__title">
            <h1 class="title">BestWeather</h1>
        </div>
    </header>
    <Search 
  :search1="search1" 
  @update:search1="val => search1 = val"
  @search="handleSearch"
/>
        <div class="start-inf__block">
            <p class="start-inf__p1">Погода в вашем городе</p>
            <p class="start-inf__p2">Планируйте день с самым точным прогнозом</p>
        </div>
    </div>

        

    
   
</body>
</template>

<style>
@import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:ital,wght@0,200..800;1,200..800&family=Roboto:ital,wght@0,100..900;1,100..900&family=Rubik+Marker+Hatch&display=swap');
body {
  margin: 0;
  min-height: 100vh;
  background-image: linear-gradient(180deg, #C9E5FF, #00AAFF);
  background-repeat: no-repeat;
  background-attachment: fixed;
}

header{
        height: 100px;
        display: flex;
        justify-content: center;
        align-items: center;
        gap: 10px;
        background-color: #EBECF0;
    }
    .title{
        font-size: px;
        font-family: "Plus Jakarta Sans", sans-serif;
        font-optical-sizing: auto;
        font-weight: 400;
        font-style: normal;
    }
    @media (max-width: 767px){
        .title{
            font-size: 30px;
        }
        .header__logo-img{
            width: 60px;
            height: 60px;
        }
    }
    @media (max-width: 50px){
        .title{
            font-size: 1px;
        }
        .header__logo-img{
            width: 2px;
            height: 2px;
        }
    }

    .container{
        background-image: url(images/fon.png);
        height: calc(100vh- 100px );

        background-repeat: no-repeat;
        background-size: cover;
    }
    .start-inf__block{
        padding-left: 70px;
        display: flex;
        flex-direction: column;
        gap: 0px;
    }
    .start-inf__p1{
        font-size: 100px;
        color: #0016E0;
        font-family: "Rubik Marker Hatch", system-ui;
        font-weight: 400;
        font-style: normal;
        width: 450px;
        line-height: 80px;
        margin-bottom: 20px;
    }
    .start-inf__p2{
        font-size:40px;
        color: #0010A3;
        font-family: "Roboto", sans-serif;
        font-optical-sizing: auto;
        font-weight: 300;
        font-style: normal;
        width: 490px;
        margin-top: 20px;
        margin-bottom: 100px;
    }

</style>
