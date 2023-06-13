<template>
  <div class="flex flex-col flex-1 items-center
  ">

  <div class="text-white p-4 bg-weather-secondary w-full text-center" v-if="route.query.preview">
    <p>
      You are currently previewing this city, click the "+"
        icon to start tracking this city.
    </p>
  </div>

  <div v-if="liveWeather"
  class="flex flex-col items-center text-white py-12">
    <h1 class="text-4xl mb-4">{{route.params.district}}</h1>

    <p class="text-sm mb-12">
      {{liveWeather.reporttime.split(' ')[0]}}
    </p>
    <p class="text-8xl mb-8 ml-8">
      {{liveWeather.temperature}}&deg;
    </p>
    <p  class="text-2xl mb-4">
      {{liveWeather.weather}}
    </p>
    <p>
      风向：
      {{liveWeather.winddirection }}
    </p>
  </div>
  <hr class="border-white border-opacity-10 border w-full" />

  <div v-if="foreWeather"
    class=" w-full py-12">  
    <div class="mx-8 text-white">
      <h2 class="mb-4 text-xl">天气预测:</h2>
      
      <div class="flex justify-between">
        <div v-for="(item,index) in foreWeather" :key="index"
        class="text-sm">
          <p class="text-center text-lg mb-2">
            {{item.date.split('-')[2]}}号
          </p>
          <p>
            白天：
            {{item.dayweather}}
          </p>
          <p>
            气温：
            {{ item.daytemp }}&deg;
          </p>
          <p>
            夜晚：
            {{item.nightweather}}
          </p>
          <p>
            气温：
            {{
              item.nighttemp
            }}&deg;
          </p>
            </div>
      </div>

    </div>
  </div>

</div>

  <div class="flex justify-center items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500" @click="removeCity">
    <i class="fa-solid fa-trash"></i>
    <p>Remove City</p>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import { useRoute,useRouter } from 'vue-router';

const route = useRoute()
const liveWeather = ref(null)
const foreWeather = ref(null)

const getWeatherData = async () => {
  try {
    const liveWeatherResponse = await fetch(`https://restapi.amap.com/v3/weather/weatherInfo?key=79f62c120f52839c6f67d6cd0280e50f&city=${route.params.adcode}&extensions=base`)

    const liveData = await liveWeatherResponse.json()

    console.log(liveData);

    liveWeather.value = liveData.lives[0]



    const forecastWeatherResponse = await fetch(`https://restapi.amap.com/v3/weather/weatherInfo?key=79f62c120f52839c6f67d6cd0280e50f&city=${route.params.adcode}&extensions=all`)

    const { forecasts } = await forecastWeatherResponse.json()

    foreWeather.value = forecasts[0].casts

    console.log(foreWeather);

  } catch (error) {
    console.log(error);
  }
}
await getWeatherData()

const router = useRouter()

const removeCity = () => { 
  const cities = JSON.parse(localStorage.getItem('savedCities'))

  const updatedCities = cities.filter((city) => { 
    city.id = route.query.id
  })

  localStorage.setItem('savedCities', JSON.stringify(updatedCities))

  router.push({
    name:'home'
  })
}




</script>

<style lang="scss" scoped>

</style>