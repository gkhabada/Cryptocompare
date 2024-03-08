<template>
  <div class="coins">
    <h1>Test work by <a href="http://gkhabada.h1n.ru" target="_blank">Gadzhidadaev Gadzhi</a>. </h1>
    <h2>Show the top cryptocurrency and their prices online</h2>
    <hr>
    <button type="button" name="button" @click="sorted('max')" v-if="sortedStatus === 'min'">sorting from min</button>
    <button type="button" name="button" @click="sorted('min')" v-else>sorting from max</button>
    <table>
      <thead>
        <th>№</th>
        <th>logo</th>
        <th>name</th>
        <th>fullname</th>
        <th>price</th>
      </thead>
      <tbody>
        <tr v-for="(coin, index) in coins" :key="index">
          <td>{{ index + 1 }}</td>
          <td><img :src="'https://www.cryptocompare.com' + coin.img" :alt="coin.name"></td>
          <td>{{ coin.name }}</td>
          <td>{{ coin.fullname }}</td>
          <td>{{ (+coin.price).toFixed(3) }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import io from 'socket.io-client';

export default {
  name: 'TheCoins',
  mounted() {
    fetch(
      `https://min-api.cryptocompare.com/data/top/mktcapfull?limit=50&tsym=USD&sign=true${this.apiKey}`,
      { method: 'GET' }
    )
      .then((response) => response.json())
      .then((response) => {
        this.crypto = response.Data
        this.parseApiData(this.crypto)
        this.sorted('min')
      });
  },
  data() {
    return {
      coins: '',
      crypto: '',
      apiKey: '&api_key={993cdddb26568296d1e11b6283fd7c62e595be4d93693991a4aa6e1ab6c247c6}',
      cryptoStreamUrl: 'wss://streamer.cryptocompare.com',
      sortedStatus: 'min'
    }
  },
  methods: {
    parseApiData: function (coins) {
      let coinsParsed = []

      this.crypto.forEach(coin => {
        if (coin.DISPLAY && coin.CoinInfo) {
          coin = {
            img: coin.CoinInfo.ImageUrl,
            name: coin.CoinInfo.Name,
            fullname: coin.CoinInfo.FullName,
            price: (coin.DISPLAY.USD.PRICE || coin.DISPLAY.PRICE || 0).replace(/[,\,$\, ]/g, '')
          }
          coinsParsed.push(coin)
        }
      })
      this.coins = coinsParsed
      let coinsNameArray = []
      this.coins.forEach(function (item) {
        coinsNameArray.push(item.name)
      })
      this.subscribeStream(coinsNameArray);
    },
    subscribeStream: function (names) {
      const cryptoio = io(this.cryptoStreamUrl);
      const subscriptions = [];

      names.forEach((name) => {
        subscriptions.push('5~CCCAGG~' + name + '~USD');
      });

      cryptoio.emit('SubAdd', {
        'subs': subscriptions,
      });

      cryptoio.on('m', message => {
        this.handleMessage(message);
      });
    },
    handleMessage: function (message) {
      message = message.split('~');
      if ((message[4] === "1") || (message[4] === "2")) {
        this.coins.forEach(function (item) {
          item.name === message[2] ? item.price = message[5] : item.price;
        });
      }
    },
    sorted: function (to) {
      const compare = (a, b) => {
        a = Number(a.price.replace(/[,\,$\, ]/g, ''))
        b = Number(b.price.replace(/[,\,$\, ]/g, ''))

        if (to === 'max') {
          this.sortedStatus = 'max'
          if (a < b) return -1;
          if (a > b) return 1;
          return 0;
        } else if (to === 'min') {
          this.sortedStatus = 'min'
          if (a > b) return -1;
          if (a < b) return 1;
          return 0;
        }
      }
      return this.coins.sort(compare);
    }
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
