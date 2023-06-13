<template>
  <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary">
    <SiteNavigation />

    <RouterView class="flex-1" v-slot="{ Component }">
      <transition name="page">
        <component :is="Component"></component>
      </transition>
    </RouterView>
  </div>
</template>

<script setup>
import { RouterView } from 'vue-router';
import SiteNavigation from "./components/SiteNavigation.vue";
</script>

<style>
.page-enter-active{
  transition: all .6s ease;
}
.page-enter-from{
  opacity: 0;
}

</style>