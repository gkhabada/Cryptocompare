<template>
<div class="coins">
  <button type="button" name="button" @click="sorted('min')">sorting to min</button>
  <button type="button" name="button" @click="sorted('max')">sorting to max</button>
  <table>
    <thead>
      <th>№</th>
      <th>logo</th>
      <th>name</th>
      <th>fullname</th>
      <th>price</th>
    </thead>
    <tbody>
      <tr v-for="(coin, index) in newCrypto" :key="index">
        <td>{{index + 1}}</td>
        <td><img :src="'https://www.cryptocompare.com' + coin.img" :alt="coin.name"></td>
        <td>{{ coin.name }}</td>
        <td>{{ coin.fullame }}</td>
        <td>{{ coin.price }}</td>
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
      .then(response => {
        this.crypto = response.data.Data
        this.parseApiData(this.crypto)
      });
  },
  data() {
    return {
      newCrypto: '',
      crypto: '',
      apiKey: '&api_key={993cdddb26568296d1e11b6283fd7c62e595be4d93693991a4aa6e1ab6c247c6}',
      cryptoStreamUrl: 'https://streamer.cryptocompare.com'
    }
  },
  methods: {
    parseApiData: function(coins) {

      let coinsParsed = {}

      this.crypto.forEach(coin => {
        coin = {
          img: coin.CoinInfo.ImageUrl,
          name: coin.CoinInfo.Name,
          fullname: coin.CoinInfo.FullName,
          price: coin.DISPLAY.USD.PRICE.replace(/[,\,$\, ]/g, '')
        }
        coinsParsed[coin.name] = coin
      })
      this.newCrypto = coinsParsed
      this.subscribeStream(Object.keys(coinsParsed))
    },
    subscribeStream: function(names) {
      let cryptoio = io.connect(this.cryptoStreamUrl)
      let subscriptions = []

      names.forEach(name => {
        subscriptions.push('5~CCCAGG~' + name + '~USD')
      })

      cryptoio.emit('SubAdd', {
        'subs': subscriptions
      })

      cryptoio.on('m', message => {
        this.handleMessage(message)
      })
    },
    handleMessage: function(message) {
      message = message.split('~')

      if ((message[4] === "1") || (message[4] === "2")) {

        let coinName = message[2]
        this.newCrypto[coinName].price = message[5]
      }
    },
    sorted: function(to) {
      function compare(a, b) {
        console.log(111)
        a = Number(a.price.replace(/[,\,$\, ]/g, ''))
        b = Number(b.price.replace(/[,\,$\, ]/g, ''))

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

      console.log(this.newCrypto)

      return this.newCrypto.sort(compare);
    },
  }
}
</script>

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
