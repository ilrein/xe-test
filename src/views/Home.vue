<template>
  <sui-container>
    <sui-header
      class="header"
    >
      XE Test
    </sui-header>

    <Table
      :currencies="currencies"
      :rates="rates"
      :list="list"
      :timestamp="timestamp"
      :amount="amount"
      :loading="loading"
      @onChange="changeAmount"
    />
  </sui-container>
</template>

<script>
import request from 'request';

import Table from '@/components/Table.vue';

const auth = {
  user: process.env.VUE_APP_ACCOUNT_API,
  pass: process.env.VUE_APP_ACCOUNT_PW,
};

const defaultList = [
  {
    currency: 'USD',
    countryName: 'us',
  },
  {
    currency: 'EUR',
    countryName: 'eu',
  },
  {
    currency: 'GBP',
    countryName: 'gb uk',
  },
  {
    currency: 'INR',
    countryName: 'in',
  },
];

export default {
  name: 'Home',
  components: {
    Table,
  },
  data() {
    return {
      loading: true,
      timestamp: '',
      currencies: [],
      rates: [],
      list: defaultList,
      amount: 1,
      inverse: false,
    };
  },
  computed: {
    from() {
      return 'CAD';
    },
    to() {
      return this.list.map((val) => val.currency).join(',');
    },
  },
  mounted() {
    this.getCurrencies();
    this.getHistoricRates();
    this.getInverse();
  },
  methods: {
    getCurrencies() {
      const options = {
        url: 'https://xecdapi.xe.com/v1/currencies.json',
        auth,
      };

      request(options, (error, response, body) => {
        if (!error && response.statusCode === 200) {
          this.currencies = JSON.parse(body).currencies;
        }
      });
    },
    getHistoricRates() {
      this.loading = true;

      const options = {
        url: `https://xecdapi.xe.com/v1/convert_from.json/?from=${this.from}&to=${this.to}&amount=${this.amount}`,
        auth,
      };

      request(options, (error, response, body) => {
        if (!error && response.statusCode === 200) {
          this.timestamp = JSON.parse(body).timestamp;
          this.rates = JSON.parse(body).to;
        }
      });

      this.loading = false;
    },
    getInverse() {
      this.loading = true;

      const options = {
        url: `https://xecdapi.xe.com/v1/convert_to.json/?to=${this.from}&to=${this.from}&amount=${this.amount}`,
        auth,
      };

      request(options, (error, response, body) => { // eslint-disable-line
        if (!error && response.statusCode === 200) {
          // console.log(JSON.parse(body));
          // this.rates = JSON.parse(body).to;
        }
      });

      this.loading = false;
    },
    changeAmount(val) {
      this.amount = Number(val);
      this.rates = [];
      this.getHistoricRates();
    },
  },
};
</script>

<style lang="scss" scoped>
.header {
  margin-top: 1rem !important;
  font-size: 40px !important;
}
</style>
