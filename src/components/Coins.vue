<template>
<div class="coins">
  <button type="button" name="button" @click="  sorted('min')">sorting to min</button>
  <button type="button" name="button" @click="  sorted('max')">sorting to max</button>
  <table>
    <thead>
      <th>№</th>
      <th>logo</th>
      <th>name</th>
      <th>fullname</th>
      <th>price</th>
    </thead>
    <tbody>
      <tr v-for="(coin, index) in crypto" :key="index">
        <td>{{index + 1}}</td>
        <td><img :src="'https://www.cryptocompare.com' + coin.CoinInfo.ImageUrl" :alt="coin.CoinInfo.Name"></td>
        <td>{{coin.CoinInfo.Name}}</td>
        <td>{{coin.CoinInfo.FullName}}</td>
        <td>{{coin.DISPLAY.USD.PRICE}}</td>
      </tr>
    </tbody>
  </table>
</div>
</template>

<script>
import axios from "axios";

export default {
  name: 'Coins',
  mounted() {
    axios
      .get('https://min-api.cryptocompare.com/data/top/mktcapfull?limit=50&tsym=USD&sign=true' + this.apiKey)
      .then(response => (this.crypto = response.data.Data));
  },
  data() {
    return {
      newCrypto: '',
      crypto: '',
      apiKey: '&api_key={993cdddb26568296d1e11b6283fd7c62e595be4d93693991a4aa6e1ab6c247c6}'
    }
  },
  methods: {
    sorted: function(to) {
      function compare(a, b) {
        a = Number(a.DISPLAY.USD.PRICE.replace(/[,\,$\, ]/g, ''))
        b = Number(b.DISPLAY.USD.PRICE.replace(/[,\,$\, ]/g, ''))

        if (to === 'max') {
          if (a < b)
            return -1;
          if (a > b)
            return 1;
          return 0;
        } else if (to === 'min') {
          if (a > b)
            return -1;
          if (a < b)
            return 1;
          return 0;
        }
      }

      return this.crypto.sort(compare);
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
table {
  text-align: left;
  width: 100%;
  padding: 10px 10%;
  margin-bottom: 20px;
}

th {
  padding: 10px 0;
}

th,
td {
  border-bottom: 1px solid #ddd;
}

table tbody img {
  width: 50px;
  height: 50px;
  object-fit: contain;
}
</style>
