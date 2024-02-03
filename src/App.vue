<script setup>
import { RouterView } from 'vue-router'
/*
import { RouterLink, RouterView } from 'vue-router'
import HelloWorld from './components/HelloWorld.vue'
*/
import HeaderComponents from '@/components/Header/Header.vue'

</script>

<template>
  <HeaderComponents/>
  <RouterView />
</template>

<style lang="sass" src="@/assets/sass/style.sass"></style>
