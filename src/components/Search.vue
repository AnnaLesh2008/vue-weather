<script setup>
import axios from 'axios'
import debounce from 'lodash/debounce'
import { ref } from 'vue';
const props = defineProps({
  search1: String
});

const emit = defineEmits(['update:search1', 'search']);
const citySuggestions = ref([]); 
const handleInput = debounce(async(e) => {
  emit('update:search1', e.target.value);
  try{
    const cities = await axios.get(`http://api.openweathermap.org/geo/1.0/direct?q=${e.target.value}&limit=10&appid=67c42fdd3811877ec78f29537225fd87&lang=ru`)
    citySuggestions.value = cities.data
  }
  catch (error){
    console.log('Ошибка')
    citySuggestions.value = []
  }
  
}, 300);

const handleKeyUp = (e) => {
  if (e.key === 'Enter') {
    emit('search');
  }
};

const selectCity = (city) => {
  emit('update:search1', `${city.name}, ${city.state || city.country}`);
  citySuggestions.value = [];
  // Передаем объект с координатами
  emit('search', { 
    lat: city.lat, 
    lon: city.lon,
    // Добавляем полное название для WeatherAPI
    fullName: `${city.name}, ${city.state || city.country}`
  });
};


</script>

<template>
  <div class="search-container">
    <div class="section-search">
      <input 
        class="search" 
        @keyup="handleKeyUp"
        @input="handleInput"
        :value="search1" 
        type="text" 
        placeholder="Введите город"
      >    
      <div class="suggestions-wrapper" v-if="citySuggestions.length">
        <ul class="suggestions">
          <li 
            v-for="city in citySuggestions" 
            :key="city.id"
            @click="selectCity(city)"
          >
            {{ city.name }}, {{ city.state }}
          </li>
        </ul>
      </div>
    </div>
  </div>
</template>

<style>
/* контейнер для всего */
.search-container {
  width: 100%;
  display: flex;
  justify-content: center;
  padding-top: 15px;
  position: relative;
}

/* секция поиска */
.section-search {
  width: 60%;
  position: relative;
}

.search {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
  outline: none;
  width: 100%;
  height: 25px;
  border-radius: 30px;
  padding-left: 10px;
  padding-right: 10px;
  border: 2px solid lightgrey;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.15);
  transition: all 0.3s ease;
}

.search:focus {
  border-color: grey;
  transform: scale(1.02);
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.15);
}

.suggestions-wrapper {
  position: absolute;
  width: 100%;
  top: 100%;
  left: 0;
  z-index: 1000;
  margin-top: 5px;
}

.suggestions {
  width: 100%;
  max-height: 200px;
  overflow-y: auto;
  border-radius: 10px;
  background-color: white;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  list-style: none;
  padding: 0;
  margin: 0;
}

.suggestions li {
  font-family: "Roboto", sans-serif;
  padding: 8px 15px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.suggestions li:hover {
  background-color: #f0f0f0;
}
</style>