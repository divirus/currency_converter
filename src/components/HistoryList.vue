<script>

import { Line } from 'vue-chartjs';

export default {
  name: 'HistoryList',
  props: ['currenciesData'],
  extends: Line,
  data() {
    return {
      startDate: this.formatDate(10),
      endDate: this.formatDate(),
      chartData: {
        labels: [],
        datasets: [
          {
            label: 'Данные по коэффициенту конвертации',
            data: [],
          },
        ],
      },
    };
  },

  watch: {
    currenciesData() {
      this.fetchCurrencyRatesHistory();
    },
  },

  methods: {
    init() {
      this.fetchCurrencyRatesHistory();
    },

    fetchCurrencyRatesHistory() {
      const { currencyFrom } = this.currenciesData;
      const { currencyTo } = this.currenciesData;

      fetch(`https://api.exchangerate.host/timeseries/?base=${currencyFrom}&symbols=${currencyTo}&start_date=${this.startDate}&end_date=${this.endDate}`)
        .then((res) => res.json())
        .then((data) => {
          this.chartData.labels = Object.keys(data.rates);
          this.chartData.datasets[0].data = Object.values(data.rates).map((x) => x[currencyTo]);
          this.renderChart(this.chartData);
        });
    },

    formatDate(substract = null) {
      const currentDate = new Date();

      if (substract) {
        currentDate.setDate(currentDate.getDate() - substract);
      }

      const datePiece = currentDate.toLocaleDateString().split('.');

      return `${datePiece[2]}-${datePiece[1]}-${datePiece[0]}`;
    },
  },

  mounted() {
    this.init();
  },
};
</script>

<style scoped>
  canvas {
    position: relative;
    height: 100% !important;
    width: 100% !important;
  }
</style>
