<template>
  <header>
    <h1>Coin prices by <a href="http://gkhabada.ru" target="_blank">Gadzhidadaev Gadzhi</a> </h1>
    <h2>Online table with cryptocurrency&nbsp;prices</h2>
  </header>
</template>

<script>
export default {
  name: 'TheHeader',
}
</script>

<style scoped>


h1 {
  margin-bottom: 30px;

  text-align: center;
  font-family: 'Urbanist', sans-serif;
  font-style: normal;
  font-weight: 500;
  font-size: 48px;
  line-height: 58px;
  color: var(--text-color);
}

h1 a {
  color: var(--primary-color);
  font-weight: 600;
  text-decoration: none;
  white-space: nowrap;
}

h1 a:hover {
  text-decoration: underline;
}

h2 {
  text-align: center;
  font-family: 'Inter', sans-serif;
  font-style: normal;
  font-weight: 400;
  font-size: 30px;
  line-height: 36px;
  color: var(--text-color);
}

@media screen and (max-width: 756px) {
  h1 {
    font-size: 32px;
    line-height: 36px;
    margin-bottom: 16px;
  }

  h2 {
    font-size: 20px;
    line-height: 26px;
  }
}

@media screen and (max-width: 400px) {
  h1 {
    font-size: 24px;
    line-height: 28px;
  }

  h2 {
    font-size: 16px;
    line-height: 20px;
  }
}

</style>
