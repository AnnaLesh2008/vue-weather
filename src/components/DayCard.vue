<script setup>
    defineProps({
        day_items: Array,
        images: {}
    })
    function dateToDay(date){
        const days = ["Вс", "Пн", "Вт", "Ср", "Чт", "Пт", "Сб"];
        const dateObject = new Date(date);
        const dayOfWeekIndex = dateObject.getDay();
        const week_d = days[dayOfWeekIndex]
        return week_d
    }
    
    
</script>

<template>
    <div class="week-inf__block">
            <div class="week-inf__card" v-for="item in day_items" :key="item.date">
                <p class="p-week-card">{{ dateToDay(item.date) }}</p>
                <div class="img-cont"><img :src="`https://openweathermap.org/img/wn/${images[item.icon]}@2x.png`" :alt="`${images[day_items[0].icon]}`" width="70px" height="70px"></div>
                <p class="p-week-card">{{ Math.round(item.maxtemp_c) }}°/{{ Math.round(item.mintemp_c) }}°</p>
            </div> 
        </div>

</template>

<style scoped>
    .week-inf__block{
        display: flex;
        flex-wrap: wrap;
        gap: 15px;
        margin: 0 auto;
        max-width: 1140px;
        justify-content: center;
    }
    .week-inf__card{
        width: 150px;
        height: 220px;
        display: flex;
        flex-direction: column;
        background-color: #EBECF0;
        border-radius: 45px;
        justify-content: center;
        align-items: center;
        gap: 5px;
    }
    .p-week-card{
        font-size: 30px;
        font-family: "Plus Jakarta Sans", sans-serif;
        font-optical-sizing: auto;
        font-weight: 300;
        font-style: normal;
        text-align: center;
        margin: 0;
        margin-bottom: 10px;
    }

    .img-cont{
        width:70px;
        height: 70px;
        border-radius: 100px;
        background-color: rgb(29, 161, 255);
        margin-bottom: 10px;
    }
    @media (max-width: 1259px) {
        .week-inf__block{
            max-width: 645px;
        }
    }

</style>