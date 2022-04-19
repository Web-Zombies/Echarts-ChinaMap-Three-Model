<template>
  <div id="app">
    <!-- <mapDome /> -->
    <!-- 地图 -->
    <threeDome /> <!-- 模型 -->
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
