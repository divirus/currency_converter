<template>
  <div class="calc-container">
    <div class="select-container">
      <b-form-select
        name="currency-from-select"
        class="currencies-selector"
        v-model="currencyFrom"
        :options="options"
        @change="onChangeCurrencyFrom"
      ></b-form-select>
      <b-form-input
        name="currency-from-input"
        class="currencies-input"
        v-model="currencyFromAmount"
        type="number" min="0"
        @change="calculate"
        @input="calculate" />
      <button class="switch-currencies" @click="switchCurrencies"></button>
      <b-form-input
        name="currency-to-input"
        class="currencies-input"
        v-model="currencyToAmount"
        type="number" min="0" disabled/>
      <b-form-select
        name="currency-to-select"
        class="currencies-selector"
        v-model="currencyTo"
        :options="options"
        @change="onChangeCurrencyTo"
      ></b-form-select>
    </div>
    <a>Курс: 1 {{ this.currencyFrom }} = {{ this.currencyToRate }} {{ this.currencyTo }}</a>
    <div class="toggle-buttons-container">
      <button class="price-list-toggle" @click="togglePriceList">Список стоимостей</button>
      <button class="history-toggle" @click="toggleHistory">История курса</button>
    </div>
    <PriceList v-show="priceListVisible"
      :currencies-data="{currencyFrom, currencyTo, currencyFromRate, currencyToRate}"></PriceList>
    <div class="history-container" v-show="historyVisible">
      <HistoryList :currencies-data="{currencyFrom, currencyTo}"></HistoryList>
    </div>
  </div>
</template>

<script>
import PriceList from '@/components/PriceList.vue';
import HistoryList from '@/components/HistoryList.vue';

export default {
  name: 'CalcForm',
  components: { HistoryList, PriceList },
  data() {
    return {
      components: {
        HistoryList,
        PriceList,
      },
      currencyFrom: 'USD',
      currencyTo: 'EUR',
      currencyFromAmount: 1,
      currencyToAmount: 0,
      currencyToRate: 0,
      currencyFromRate: 0,
      rates: 0,
      options: [],
      historyVisible: true,
      priceListVisible: true,
    };
  },

  methods: {
    fetchAvailableCurrenciesList() {
      fetch('https://api.exchangerate.host/symbols')
        .then((res) => res.json())
        .then((data) => {
          this.options = [...Object.values(data?.symbols).map(((cur) => ({
            text: cur.code,
            value: cur.code,
          })))];
        });
    },

    fetchCurrencyFromLatestRatesData() {
      fetch(`https://api.exchangerate.host/latest/?base=${this.currencyFrom}`)
        .then((res) => res.json())
        .then((data) => {
          this.rates = data?.rates;
          this.currencyToRate = data?.rates[this.currencyTo];
          this.fetchCurrencyToLatestRatesData();
          this.calculate();
        });
    },

    fetchCurrencyToLatestRatesData() {
      fetch(`https://api.exchangerate.host/latest/?base=${this.currencyTo}&symbols=${this.currencyFrom}`)
        .then((res) => res.json())
        .then((data) => {
          this.currencyFromRate = data?.rates[this.currencyFrom];
        });
    },

    onChangeCurrencyFrom(newCurrency) {
      this.currencyFrom = newCurrency;
      this.fetchCurrencyFromLatestRatesData();
    },

    onChangeCurrencyTo(newCurrency) {
      this.currencyTo = newCurrency;
      this.currencyToRate = this.rates[this.currencyTo];
      this.currencyToAmount = this.rates[this.currencyTo];
      this.calculate();
    },

    calculate() {
      this.currencyToAmount = parseInt(this.currencyFromAmount, 10)
        * this.currencyToRate.toFixed(2);
    },

    switchCurrencies() {
      const oldCurrencyFromValue = this.currencyFrom;
      const oldCurrencyToValue = this.currencyTo;

      this.currencyFrom = oldCurrencyToValue;
      this.currencyTo = oldCurrencyFromValue;

      this.fetchCurrencyFromLatestRatesData();
    },

    togglePriceList() {
      this.priceListVisible = !this.priceListVisible;
    },

    toggleHistory() {
      this.historyVisible = !this.historyVisible;
    },
  },

  mounted() {
    this.fetchAvailableCurrenciesList();
    this.fetchCurrencyFromLatestRatesData();
  },
};
</script>

<style scoped>
  a {
    text-align: center;
    margin: 6px;
    width: 200px;
  }

  input {
    width: 100%;
    padding-left: 4px;
  }

  .calc-container {
    display: flex;
    flex-direction: column;
    margin: 0 auto;
    align-items: center;
    width: 25%;
  }

  .select-container {
    display: inline-flex;
    flex-direction: row;
    height: 40px;
    min-width: 350px;
    justify-content: center;
  }

  .switch-currencies {
    min-width: 10%;
    background: center url("../assets/switch_currencies.svg") no-repeat;
  }

  .currencies-selector {
    max-width: 45%;
  }

  .toggle-buttons-container {
    display: inline-flex;
    flex-direction: row;
    width: 100%;
    min-width: 350px;
    justify-content: space-between;
  }

  .history-container {
    display: flex;
    width: 100%;
    min-width: 350px;
    border: 1px #000 solid;
    padding: 0 25px;
  }
</style>
