<template>
  <div>
    <!-- <div
      v-for="currency in currencies"
      :key="currency.iso"
    >
      {{ currency.iso }}
    </div> -->
    <sui-card>
      <sui-card-content>
        <sui-card-header>
          Canada <sui-flag name="ca" />
        </sui-card-header>
      </sui-card-content>
      <sui-card-content>
        Amount:
        <sui-input
          :value="amount"
          @change="onChange"
        />
      </sui-card-content>
    </sui-card>

    <sui-segment
      v-if="loading"
    >
      <sui-dimmer active inverted basic>
        <sui-loader>Loading</sui-loader>
      </sui-dimmer>
      <br />
      <br />
      <br />
    </sui-segment>

    <sui-table
      v-if="!loading"
      basic="very"
      celled
      collapsing
    >
      <sui-table-header>
        <sui-table-row>
          <sui-table-header-cell>Currency</sui-table-header-cell>
          <sui-table-header-cell>Amount</sui-table-header-cell>
        </sui-table-row>
      </sui-table-header>

      <sui-table-body>
        <sui-table-row
          v-for="rate in extendedRates"
          :key="rate.quotecurrency"
        >
          <sui-table-cell>
            <h4 is="sui-header" image>
              <sui-flag :name="rate.countryName" />
              <sui-header-content>
                {{ rate.currency.toUpperCase() }}
              </sui-header-content>
            </h4>
          </sui-table-cell>
          <sui-table-cell>
            {{ rate.mid.toFixed(2) }}
          </sui-table-cell>
        </sui-table-row>
      </sui-table-body>
    </sui-table>

    <h3>
      Currency timestamp: {{ timestamp }}
    </h3>
  </div>
</template>

<script>
export default {
  name: 'Table',
  props: {
    timestamp: {
      type: String,
      default: '',
    },
    currencies: {
      type: Array,
      default: () => [],
    },
    rates: {
      type: Array,
      default: () => [],
    },
    list: {
      type: Array,
      default: () => [],
    },
    amount: {
      type: Number,
      default: 0,
    },
    loading: {
      type: Boolean,
      default: true,
    },
  },
  data() {
    return {
      extendedRates: [],
    };
  },
  watch: {
    rates(data) {
      // console.log('rates change', data);
      this.extendedRates = [];
      // eslint-disable-next-line array-callback-return
      data.map((rate) => {
        const corollary = this.list.find((val) => val.currency === rate.quotecurrency);

        this.extendedRates = [...this.extendedRates, {
          ...rate,
          ...corollary,
        }];
      });
    },
  },
  methods: {
    onChange(e) {
      this.$emit('onChange', e.target.value);
    },
  },
};
</script>
