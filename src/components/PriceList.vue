<template>
  <div class="container">
    <div class="price-list-container">
      <div class="price-list">
        <a>{{ currenciesData.currencyFrom }} => {{ currenciesData.currencyTo }}</a>
        <table>
          <tr v-for="data in calculateFromPrices" :key="data.multiplier">
            <td class="td-multiplier">{{ data.multiplier }}</td>
            <td class="td-price">{{ data.price }}</td>
          </tr>
        </table>
      </div>
      <div class="price-list">
        <a>{{ currenciesData.currencyTo }} => {{ currenciesData.currencyFrom }}</a>
        <table>
          <tr v-for="data in calculateToPrices" :key="data.multiplier">
            <td class="td-multiplier">{{ data.multiplier }}</td>
            <td class="td-price">{{ data.price }}</td>
          </tr>
        </table>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'PriceList',
  props: ['currenciesData'],
  data() {
    return {
      multiplier: [1, 5, 10, 25, 100, 500, 1000, 5000],
    };
  },

  computed: {
    calculateFromPrices() {
      return this.multiplier.map((x) => ({
        multiplier: x,
        price: (x * this.currenciesData.currencyToRate).toFixed(2),
      }));
    },
    calculateToPrices() {
      return this.multiplier.map((x) => ({
        multiplier: x,
        price: (x * this.currenciesData.currencyFromRate).toFixed(2),
      }));
    },
  },
};
</script>

<style scoped>
  a {
    text-align: center;
  }

  table {
    margin: 6px;
  }

  .td-multiplier {
    text-align: left;
  }

  .td-price {
    text-align: right;
  }

  .container {
    display: inline-flex;
    flex-direction: column;
    width: 100%;
    min-width: 350px;
    border: 1px solid #000;
  }

  .price-list-container {
    display: inline-flex;
    flex-direction: row;
    width: 100%;
    padding: 20px;
    justify-content: space-evenly;
  }

  .price-list {
    border: 1px #000 solid;
    display: inline-flex;
    flex-direction: column;
    width: 40%;
    margin: auto;
  }
</style>
