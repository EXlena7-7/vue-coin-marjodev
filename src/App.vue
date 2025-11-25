<script>
export default {
  name: "App",
  components: {},
  data() {
    return {
      coins: [],
      filteredCoins: [],
      titles: ["Stock", "Cripto", "Precio", "Precio Cambio", "Cambio 24h"],
      textSearch: "",
      currentPage: 1,   
      perPage: 11       
    };
  },
  computed: {
    totalPages() {
      return Math.ceil(this.filteredCoins.length / this.perPage) || 1;
    },
    paginatedCoins() {
      const start = (this.currentPage - 1) * this.perPage;
      const end = start + this.perPage;
      return this.filteredCoins.slice(start, end);
    }
  },
  async mounted() {
    const res = await fetch(
      "https://api.coingecko.com/api/v3/coins/markets?vs_currency=usd&order=market_cap_desc&per_page=100&page=1&sparkline=false"
    );
    const data = await res.json();
    this.coins = data;
    this.filteredCoins = data;
  },
  methods: {
    searchCoin() {
      this.filteredCoins = this.coins.filter(
        (coin) =>
          coin.name.toLowerCase().includes(this.textSearch.toLowerCase()) ||
          coin.symbol.toLowerCase().includes(this.textSearch.toLowerCase())
      );
      this.currentPage = 1; 
    },
    changePage(page) {
      if (page < 1 || page > this.totalPages) return;
      this.currentPage = page;
    }
  }
};
</script>



<template>
  <div class="bg-dark text-light min-vh-100">
  <div class="container">
    <div class="row">
      <h1 class="text-warning">Cripto Today</h1>

      <input
        type="text"
        class="form-control text-light bg-secondary rounded-0 border-0 my-4"
        placeholder="Busqueda"
        v-model="textSearch"
        @keyup="searchCoin()"
        autofocus
      />

      <table class="table table-hover table-secondary text-light ">
        <thead>
          <tr>
            <th v-for="(title, index) in titles" :key="index">
              {{ title }}
            </th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(coin, index) in paginatedCoins" :key="coin.id">
            <td class="text-muted">{{ index }}</td>
            <td>
              <img :src="coin.image" :alt="coin.name" style="width: 2rem" class="me-2" />
              <span>
                {{ coin.name }}
              </span>
              <span class="ms-2 text-muted text-uppercase">
                {{ coin.symbol }}
              </span>
            </td>
            <td>{{ coin.current_price.toLocaleString() }}</td>
            <td
              :class="[
                coin.price_change_percentage_24h > 0
                  ? 'text-success'
                  : 'text-danger',
              ]"
            >
              {{ coin.price_change_percentage_24h }}
            </td>
            <td>{{ coin.total_volume.toLocaleString() }}</td>
          </tr>
        </tbody>
        <tbody v-if="filteredCoins.length === 0">
          
            <td :colspan="titles.length" class="text-center text-success text-muted py-4">
              <p>Cripto no encontrada!!</p>
            </td>
        
        </tbody>
      </table>

      <nav v-if="totalPages > 1" aria-label="Page navigation" class="mt-3">
        <ul class="pagination justify-content-center">
          
          <li class="page-item" :class="{ disabled: currentPage === 1 }">
            <button
              class="page-link bg-dark text-light border-secondary"
              @click="changePage(currentPage - 1)"
              aria-label="Previous"
            >
              <span aria-hidden="true">&laquo;</span>
              <span class="visually-hidden">Anterior</span>
            </button>
          </li>

          <li
            v-for="page in totalPages"
            :key="page"
            class="page-item"
            :class="{ active: page === currentPage }"
          >
            <button
              class="page-link bg-dark text-light border-secondary"
              @click="changePage(page)"
            >
              {{ page }}
            </button>
          </li>

         
          <li class="page-item" :class="{ disabled: currentPage === totalPages }">
            <button
              class="page-link bg-dark text-light border-secondary"
              @click="changePage(currentPage + 1)"
              aria-label="Next"
            >
              <span aria-hidden="true">&raquo;</span>
              <span class="visually-hidden">Siguiente</span>
            </button>
          </li>
        </ul>
      </nav>
    </div>
  </div>
  </div>
</template>


<style>
</style>
