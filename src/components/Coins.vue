<template>
<div class="coins">
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
    // upDate(name) {
    //   this.polling = setInterval(() => {
    //     axios
    //       .get('https://min-api.cryptocompare.com/data/price?tsyms=USD,EUR&fsym=' + name + this.apiKey)
    //       .then(response => (this.newCrypto = response.data.USD));
    //     console.log(this.newCrypto)
    //     return
    //   }, 10000)
    // }
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

th, td {
  border-bottom: 1px solid #ddd;
}

table tbody img {
  width: 50px;
  height: 50px;
  object-fit: contain;
}
</style>
