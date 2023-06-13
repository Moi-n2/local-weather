<template>
  <main class="container text-white">

    <div class="pt-4 mb-8 relative">
      <input type="text"
      v-model="searchQuery"
      @input="getSearchResults"
      placeholder="Search for a city or state"
      class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]">

      <ul>
        <p v-if="error">Sorry, something went wrong, please try again.</p>

        <p v-if="!error && nomatch">No results match your query, try a different term.</p>

        <template v-else>
          <li
          class="py-2 cursor-pointer"
          v-for="item in tipList.slice(0,5)"
          :key="item.id"
          @click="previewCity(item)"
          >
          {{item.name}}
        </li>
        </template>
      </ul>
    </div>

    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList>
        </CityList>

        <template #fallback>
          <CityCardSkeleton />
        </template>
      </Suspense>
    </div>


  </main>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import CityList from '../components/CityList.vue'
import CityCardSkeleton from '../components/CityCardSkeleton.vue';


const searchQuery = ref('')
const queryTimeout = ref(0)
const tipList = ref([])
const error = ref('')
const nomatch = ref(false)

const router = useRouter()
const previewCity = (city) => {
  router.push({
    name: 'cityView',
    params: { adcode: city.adcode, district: city.district },
    query: { 
      preview: true,
    }
  })
 }

const getSearchResults = () => { 
  nomatch.value = false
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (!searchQuery.value) { 
      tipList.value = []
      return
    }

    try {
      const response = await fetch(`https://restapi.amap.com/v3/assistant/inputtips?key=79f62c120f52839c6f67d6cd0280e50f&keywords=${searchQuery.value}`)

      const data = await response.json()

      console.log(data);

      if (data.count === '0') { 
        nomatch.value = true
        return
      }

      tipList.value = data.tips

    } catch (err) {
      error.value = err
    }
     
  }, 300);
}



</script>

<style lang="scss" scoped>

</style>
