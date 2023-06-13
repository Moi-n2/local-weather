<template>

  <div v-for="(city, index) in cityLists" :key="index">
    <CityCard :city="city" @click="checkCityView(city)"></CityCard>
  </div>
    
</template>

<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import CityCard from '../components/CityCard.vue'


const cityLists = ref([])
const savedCities = ref(null)
const getCities = async () => { 
  if (localStorage.getItem('savedCities')) { 
    savedCities.value = JSON.parse(
      localStorage.getItem('savedCities')
    )
  }

  const requests = []

  savedCities.value.forEach((city) => { 
    requests.push(fetch(`https://restapi.amap.com/v3/weather/weatherInfo?key=79f62c120f52839c6f67d6cd0280e50f&city=${city.adcode}&extensions=base`).then(res => res.json()).then(data => { 
      data.lives[0].district = city.district
      return data.lives[0]
    }))
  })

  cityLists.value = await Promise.all(requests)
}

await getCities()

const router = useRouter()
const checkCityView = (city) => { 
  router.push({
    name: 'cityView',
    params: { adcode: city.adcode, district: city.district },
    query: {
      id:city.id
    }
  })
}

</script>

<style lang="scss" scoped>

</style>