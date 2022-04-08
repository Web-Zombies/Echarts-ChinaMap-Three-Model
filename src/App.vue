<template>
  <div id="app">
    <mapDome />
    <!-- <threeDome /> -->
  </div>
</template>

<script>
import mapDome from './components/mapDome.vue'
import threeDome from './components/threeDome.vue'
export default {
  name: 'App',
  components: {
    mapDome,
    threeDome
  }
}
</script>

<style>
#app {
  margin: 0;
  padding: 0;
}
</style>
