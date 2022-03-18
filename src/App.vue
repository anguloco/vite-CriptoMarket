<script setup>
import { ref } from "vue";

let counter = ref(0);

setInterval(() => {
  counter.value++;
}, 1000);
</script>

<script>
export default {
  methods: {
    searchCoin() {
      // DO FILTER WITH INCLUDES AND LOWERCASE 
      this.filteredCoins = this.coins.filter((coin) =>
        coin.name.toLowerCase().includes(this.textSearch.toLowerCase())
        || 
        coin.symbol.toLowerCase().includes(this.textSearch.toLowerCase())

      );
    },
  },
  name: "App",
  data() {
    return {
      filteredCoinds: [],
      coins: [],
      titles: [
        "#",
        "Coin",
        "Price",
        "Price Change",
        "Low in 24h",
        "Last Updated",
      ],
      textSearch: "",
    };
  },

  async mounted() {
    const res = await fetch(
      "https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false"
    );
    const data = await res.json();
    console.log(data);
    this.coins = data;
    this.filteredCoins = data;
  },
};
</script>

<template>
  <section class="input search-coin">
    <div>
      <div class="row">
        <h1>Coin Market</h1>

        <input
          type="text"
          class="fancysearch"
          placeholder="Search Coin"
          v-model="textSearch"
          @keyup="searchCoin()"
        />
      </div>
    </div>
  </section>
  <section class="table mt-9">
    <table class="shadow-lg bg-white border-collapse">
      <tr>
        <th
          v-for="title in titles"
          :key="title"
          class="bg-blue-100 border text-left px-8 py-4"
        >
          {{ title }}
        </th>
        <!-- <th class="bg-blue-100 border text-left px-8 py-4">Contact</th> -->
      </tr>
      <tr v-for="coin in filteredCoins" :key="coin.id">
        <td class="coin-container border px-8 py-4">
          <img
            :src="coin.image"
            class=""
            style="width: 2rem"
            alt="BlockChain ICON"
          />
          <!-- SHOWS ICON AND BLOCKCHAIN NAME  -->
          <span class="exc uppercase text-blue-600">{{ coin.symbol }}</span>
        </td>
        <td class="border px-8 py-4">
          <span> {{ coin.name }} </span>
        </td>
        <td class="border px-8 py-4">
          <span> ${{ coin.current_price.toLocaleString() }} </span>
        </td>
        <td class="border px-8 py-4">
          <span
            :class="[
              coin.price_change_percentage_24h > 0
                ? 'text-green-500'
                : 'text-red-400',
            ]"
          >
            {{ coin.price_change_percentage_24h }}
          </span>
        </td>
        <td class="border px-8 py-4">
          <span> {{ coin.low_24h }} </span>
        </td>
        <td class="border px-8 py-4">
          <span> {{ coin.last_updated }} </span>
        </td>
      </tr>
    </table>
  </section>

  <!-- //default VITE VALUES GOES DOWN -->
  <div>
    <header class="bg-white shadow" v-if="$route.meta.title">
      <div class="mx-auto max-w-7xl px-4 py-6 sm:px-6 lg:px-8">
        <h1
          @click="counter = 0"
          class="text-3xl font-bold leading-tight text-gray-900"
        >
          {{ $route.meta.title }} / {{ counter }}
        </h1>
      </div>
    </header>
    <main>
      <router-view />
    </main>
  </div>
</template>

<style>
.table {
  display: flex;
  justify-content: center;
  align-items: center;
}
.coin-container {
  display: flex;
  justify-content: flex-start;
  align-items: flex-start;
  gap: 1.9rem;
}

.coin-container .exc {
  padding: 2px;
}

.search-coin {
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  margin-top: 20px;
}
.fancysearch {
  margin-top: 2rem;
}
.row h1 {
  font-weight: 800;
  font-size: 2rem;
  text-align: center;
}
</style>