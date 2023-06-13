<template>
  <div class="flex py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer">
    <div class="flex flex-col flex-1">
      <h2 class="text-2xl">{{city.district}}</h2>
      <h3>{{city.city}}</h3>
    </div>

    <div class="flex flex-col gap-2 items-end">
      <p class="text-xl">
        {{city.weather}}
      </p>
      <p class="text-sm">{{city.temperature}}&deg;</p>
    </div>

  </div>
</template>

<script setup>
defineProps({
  city: {
    type: Object,
    default: () => { }
  }
})

</script>
