<template>
<div id="app">
  <h1>Bitcoin Price Index</h1>

  <section v-if="errored">
    <p>We're sorry, we're not able to retrieve this information at the moment, please try back later</p>
  </section>

  <section v-else>
    <div v-if="loading">Loading...</div>

    <div
      v-else
      v-for="currency in info"
	:key="currency.code"
      class="currency"
    >
      {{ currency.description }}:
      <span class="lighten">
        <span v-html="currency.symbol"></span>{{ currency.rate_float | currencydecimal }}
      </span>
    </div>

  </section>
</div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'App',
  data() {
   return {
    loading: true,
    errored: false,
    coin: "BTC",
    info: null
   }
  },
filters: {
    currencydecimal (value) {
      return value.toFixed(2)
    }
  },
  methods: {
   updatePrices() {
	if (!this.errored) {
	if (this.info == null) this.loading = true;
     axios
      .get('https://api.coindesk.com/v1/bpi/currentprice.json')
      .then(response => {
        this.info = response.data.bpi
      })
      .catch(error => {
        console.log(error)
        this.errored = true
      })
      .finally(() => this.loading = false)
	}
   }
  },
  mounted() {
	this.updatePrices();
	setInterval(this.updatePrices, 1000*30);
  }
}
</script>

<style>
body {
  display: flex;
  justify-content: center;
  background: #7E8D85;
  font-family: 'Roboto Slab', serif;
  line-height: 1.4;
}

#app {
  margin-top: 20px;
  width: 300px;
  padding: 0 40px 40px;
  background: #2F242C;
  border-radius: 5px;
  color: #B3BFB8;
}

h1 {
  color: #F0F7F4;
}

.lighten {
  color: white;
}
</style>
