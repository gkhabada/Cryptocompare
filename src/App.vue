<template>
  <div id="app">
    <TheHeader/>
    <CoinsTable/>
  </div>
</template>

<script>
import TheHeader from './components/TheHeader.vue'
import CoinsTable from './components/CoinsTable.vue'

export default {
  name: 'App',
  components: {
    TheHeader,
    CoinsTable,
  }
}
</script>

<style>
#app {
  width: min(1200px, calc(100% - 32px));
  margin: 80px auto;

  display: grid;
  gap: 100px;
}

body {
  margin: 0;
}
</style>
