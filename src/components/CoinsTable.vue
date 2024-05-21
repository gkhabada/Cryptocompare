<template>
  <div class="coins">
    <table>
      <thead>
        <tr>
          <th>№</th>
          <th>Logo</th>
          <th>
            Name
            <button @click="sortCoins('name')">
              <img
                :src="sortType ==='max' ? '/public/arrow-gold.svg' : '/public/arrow-black.svg'"
                alt="max"
              >
              <img
                :src="sortType ==='min' ? '/public/arrow-gold.svg' : '/public/arrow-black.svg'"
                alt="min"
                class="rotate180"
              >
            </button>
          </th>
          <th>Fullname</th>
          <th>
            Price
            <button @click="sortCoins('price')">
              <img
                :src="sortType ==='max' ? '/public/arrow-gold.svg' : '/public/arrow-black.svg'"
                alt="max"
              >
              <img
                :src="sortType ==='min' ? '/public/arrow-gold.svg' : '/public/arrow-black.svg'"
                alt="min"
                class="rotate180"
              >
            </button>
          </th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(coin, index) in sortedCoins" :key="index">
          <td>{{ index + 1 }}</td>
          <td><img :src="'https://www.cryptocompare.com' + coin.img" :alt="coin.name"></td>
          <td>{{ coin.name }}</td>
          <td>{{ coin.fullname }}</td>
          <td>{{ (+coin.price).toFixed(2) }} $</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import io from 'socket.io-client';

const cryptoStreamUrl = 'wss://streamer.cryptocompare.com';
const apiKey = '&api_key={993cdddb26568296d1e11b6283fd7c62e595be4d93693991a4aa6e1ab6c247c6}';
const sortVariants = ['min', 'max', null];

export default {
  name: 'CoinsTable',
  created() {
    fetch(
      `https://min-api.cryptocompare.com/data/top/mktcapfull?limit=50&tsym=USD&sign=true${apiKey}`,
      { method: 'GET' }
    )
      .then((response) => response.json())
      .then((response) => {
        this.crypto = response.Data;
        this.parseApiData();
        // this.sortByPrice('min')
      });
  },
  data: () => ({
    coins: '',
    crypto: '',
    sortType: null,
    sortField: null,
  }),
  computed: {
    sortedCoins() {
      const coins = [...this.coins];

      const sortByPriceCallback = (a, b) => {
        a = Number(a.price.replace(/[,\,$\, ]/g, ''))
        b = Number(b.price.replace(/[,\,$\, ]/g, ''))

        if (this.sortType === 'max') return a > b ? 1 : -1;
        if (this.sortType === 'min') return a < b ? 1 : -1;
        return 0;
      };

      const sortByNameCallback = (a, b) => {
        if (this.sortType === 'max') {
          return a.name.localeCompare(b.name);
        }
        if (this.sortType === 'min') {
          return b.name.localeCompare(a.name);
        }
      }

      if (this.sortType && this.sortField) {
        if (this.sortField === 'name') return coins.sort(sortByNameCallback);
        if (this.sortField === 'price') return coins.sort(sortByPriceCallback);
      }

      return coins;
    }
  },
  methods: {
    parseApiData() {
      let coinsParsed = []

      this.crypto.forEach((coin) => {
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
    subscribeStream(names) {
      const cryptoio = io(cryptoStreamUrl);
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
    handleMessage(message) {
      message = message.split('~');
      if ((message[4] === "1") || (message[4] === "2")) {
        this.coins.forEach(function (item) {
          item.name === message[2] ? item.price = message[5] : item.price;
        });
      }
    },

    sortCoins(field) {
      if (this.sortField === field) {
        const nextSortTypeIndex = (sortVariants.indexOf(this.sortType) + 1) % 3;
        this.sortType = sortVariants[nextSortTypeIndex];
      } else {
        this.sortType = sortVariants[0];
      }

      this.sortField = field;
    },
  },
};
</script>

<style scoped>
.coins {
  width: 100%;
  overflow-x: auto;
}

table {
  text-align: left;
  width: 100%;
  border-collapse: separate;
  border-spacing: 0;
  min-width: 400px;
}

thead th {
  padding: 0 10px;
  border-bottom: 1px solid #F4F4F4;

  font-family: 'Inter';
  font-style: normal;
  font-weight: 700;
  font-size: 20px;
  line-height: 24px;
  color: #DBAB00;
}

thead th button {
  display: inline-flex;
  flex-direction: column;
  justify-content: center;
  gap: 5px;

  padding-left: 8px;

  background: none;
  border: none;
  vertical-align: middle;
  cursor: pointer;
}

tr {
  height: 70px;
  border-radius: 10px;
}

tbody tr:nth-of-type(even) {
  background-color: #F4F4F4;
}

tbody tr:hover {
  background-color: #FDFBF2;
}

tbody td {
  padding: 0 10px;

  font-family: 'Inter', sans-serif;
  font-style: normal;
  font-weight: 400;
  font-size: 18px;
  line-height: 22px;
  color: #2A2A2A;
}

tbody td:first-child {
  border-radius: 10px 0 0 10px;
}

tbody td:last-child {
  border-radius:  0 10px 10px 0;
  white-space: nowrap;
}

tbody td img {
  width: 46px;
  height: 46px;
  object-fit: cover;
  border-radius: 50%;
}

.rotate180 {
  transform: rotate(180deg);
}

@media screen and (max-width: 756px) {
  tr {
    height: 50px;
  }

  thead th {
    padding: 0 8px;
    font-size: 16px;
    line-height: 20px;
  }

  tbody td {
    padding: 0 8px;
    font-size: 16px;
    line-height: 20px;
  }

  thead th:nth-of-type(4),
  tbody td:nth-of-type(4) {
    display: none;
  }

  tbody td img {
    width: 32px;
    height: 32px;
  }
}
</style>
