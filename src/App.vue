<template>
  <div id="app">
    <TheCoins/>
  </div>
</template>

<script>
import TheCoins from './components/TheCoins.vue'

export default {
  name: 'App',
  components: {
    TheCoins
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
