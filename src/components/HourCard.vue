<script setup>
defineProps({
  hour_items: {}
})
</script>

<template>
  <div class="weather-time__block" v-if="hour_items.list">
    <div v-for="item in hour_items.list.slice(0,8)" :key="item.dt"
     
      class="weather-time__card">
      <div class="weather-time__card-left">
        <p class="p-card">
          {{ item.dt_txt.split(' ', 2)[1].slice(0, -3) }}
        </p>
        <h2 class="h2-card">
          {{ Math.round(item.main.temp) }}°
        </h2>
      </div>
      <div class="weather-time__card-right">

        <div class="weather-time-img">
          <img 
          class="image-card" 
          :src="`https://openweathermap.org/img/wn/${item.weather[0].icon}@2x.png`" 
          width="110px"/>
        </div>
        
      </div>
    </div>
  </div>
</template>

<style scoped>
    .weather-time__block{
        display: flex;
        flex-wrap: wrap;
        gap: 20px;
        margin: 0 auto;
        max-width: 1280px;
        padding-top: 20px;
        margin-bottom: 20px;
    }
    .weather-time__card{
        display: flex;
        justify-content: space-around;
        border-radius: 45px;
        background-color: #EBECF0;
        width: 300px;
        height: 200px;
    }
    .weather-time__card-left{
        display: flex;
        flex-direction: column;
        justify-content: center;
        
    }
    .weather-time__card-right{
        display: flex;
        flex-direction: column;
        justify-content: center;
    }
    .p-card{
        font-size: 40px;
        font-family: "Plus Jakarta Sans", sans-serif;
        font-optical-sizing: auto;
        font-weight: 300;
        font-style: normal;
        margin-bottom: 15px;
    }
    .h2-card{
        font-size: 50px;
        font-family: "Plus Jakarta Sans", sans-serif;
        font-optical-sizing: auto;
        font-weight: 600;
        font-style: normal;
    }
    @media (max-width: 1259px) {
        .weather-time__block{
            max-width: 620px;
        }
    }
    .weather-time-img{
      width: 110px;
      height: 110px;
      border-radius: 70px ;
      background-color: rgb(29, 161, 255);
      text-align: center;
    }

</style>