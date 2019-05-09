<template>
  <div id="app">
    <Coins/>
  </div>
</template>

<script>
import Coins from './components/Coins'

export default {
  name: 'App',
  components: {
    Coins
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
