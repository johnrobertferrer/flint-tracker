<template>
  <v-container>
    <v-row>
      <v-col
        cols="12"
        sm="12"
      >
        <v-sheet
          rounded="lg"
        >
          <datatable 
            :data="rebuildDatatableData"
            :headers="datatable.headers"
            :settings="datatable.settings"
          />
        </v-sheet>
      </v-col>

      <v-col
        cols="12"
        sm="12"
      >
        <v-sheet
          rounded="lg"
          class="py-4 px-2 d-flex flex-column flex-md-row"
        >
          <v-card
            color="#009688"
            dark
            class="flex-grow-1 mx-1"
          >
            <v-card-title class="text-subtitle-1">
              Top Up
            </v-card-title>
            <v-card-subtitle>{{ getTotalTopUp() }}</v-card-subtitle>
          </v-card>

          <v-card
            color="#009688"
            dark
            class="flex-grow-1 mx-1"
          >
            <v-card-title class="text-subtitle-1">
              Withdrawal
            </v-card-title>
            <v-card-subtitle>{{ getTotalWithdrawal() }}</v-card-subtitle>
          </v-card>

          <v-card
            color="#009688"
            dark
            class="flex-grow-1 mx-1"
          >
            <v-card-title class="text-subtitle-1">
              Incentives
            </v-card-title>
            <v-card-subtitle>{{ getTotalIncentives() }}</v-card-subtitle>
          </v-card>

          <v-card
            color="#009688"
            dark
            class="flex-grow-1 mx-1"
          >
            <v-card-title class="text-subtitle-1">
              Net Payout
            </v-card-title>
            <v-card-subtitle>{{ getTotalNetPayout() }}</v-card-subtitle>
          </v-card>

          <v-card
            color="#009688"
            dark
            class="flex-grow-1 mx-1"
          >
            <v-card-title class="text-subtitle-1">
              Remaining Payout
            </v-card-title>
            <v-card-subtitle>{{ getTotalRemainingPayout() }}</v-card-subtitle>
          </v-card>

          <v-card
            color="#009688"
            dark
            class="flex-grow-1 mx-1"
          >
            <v-card-title class="text-subtitle-1">
              Invested Amount
            </v-card-title>
            <v-card-subtitle>{{ getTotalInvestedAmount() }}</v-card-subtitle>
          </v-card>

          <v-card
            color="#009688"
            dark
            class="flex-grow-1 mx-1"
          >
            <v-card-title class="text-subtitle-1">
              Net Earnings
            </v-card-title>
            <v-card-subtitle>{{ getTotalNetEarnings() }}</v-card-subtitle>
          </v-card>
        </v-sheet>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
  import axios from 'axios';
  import TransactionsDatatable from '../components/datatables/TransactionsDatatable.vue';

  export default {
    name: 'Transactions',

    components: {
      'datatable': TransactionsDatatable
    },

    mounted() {
      console.log('axios mounted');
      axios.get(`https://pselookup.vrymel.com/api/stocks/`, {
        headers: {
          'Access-Control-Allow-Origin': '*'
        }
      })
      .then(response => {
        console.log(response.data)
      })
      .catch(e => {
        console.log(errors)
      })
    },

    data() {
      return {
        datatable: {
          headers: [
            { text: 'Date', align: 'start', value: 'date' },
            { text: 'Details', align: 'start', sortable: false, value: 'details' },
            { text: 'Top Up', value: 'top_up' },
            { text: 'Withdrawal', value: 'withdrawal' },
            { text: 'Incentives', value: 'incentives' },
            { text: 'Net Payout', value: 'net_payout' },
            { text: 'Project Name', value: 'project_name' },
            { text: 'Invested Amount', value: 'invested_amount' },
            { text: 'Net Earnings', value: 'net_earnings' },
            { text: 'Actions', value: 'actions', sortable: false },
          ],
          data: [
            {
              date: '2021-06-05',
              details: 'test 1',
              top_up: 159,
              withdrawal: 6.0,
              incentives: 2423,
              net_payout: 4.0,
              project_name: 'Candon Fire Station 2',
              invested_amount: 5000,
              net_earnings: 300
            },
            {
              date: '2021-06-05',
              details: 'test 2',
              top_up: 237,
              withdrawal: 9.0,
              incentives: 3721,
              net_payout: 4.3,
              project_name: 'Fire Station 1',
              invested_amount: 5000,
              net_earnings: 300
            }
          ],
          settings: {
            items_per_page: 5
          }
        }
      }
    },

    methods: {
      formatNumeric(number) {
        if (isNaN(number) || number === null) { return null; }
        return Number(parseFloat(number).toFixed(2)).toLocaleString('en');
      },
      parseNumeric(number) {
        if (isNaN(number) || number === null) { return null; }
        // return parseFloat(number.replace(/,/g, ''));
        // console.log('number', number);
        return number;
      },
      sumTwoItems(a, b) {
        if (typeof a === 'string') { a = parseFloat(a.replace(/[^\d\.\-]/g, "")); }
        if (typeof b === 'string') { b = parseFloat(b.replace(/[^\d\.\-]/g, "")); }
        return a + b;
      },
      getTotalTopUp() {
        return this.formatNumeric(this.datatable.data.map(data => data.top_up).reduce((prev, next) => this.sumTwoItems(prev, next)));
      },
      getTotalWithdrawal() {
        return this.formatNumeric(this.datatable.data.map(data => data.withdrawal).reduce((prev, next) => this.sumTwoItems(prev, next)));
      },
      getTotalIncentives() {
        return this.formatNumeric(this.datatable.data.map(data => data.incentives).reduce((prev, next) => this.sumTwoItems(prev, next)));
      },
      getTotalNetPayout() {
        return this.formatNumeric(this.datatable.data.map(data => data.net_payout).reduce((prev, next) => this.sumTwoItems(prev, next)));
      },
      getTotalRemainingPayout() {
        return this.formatNumeric(this.datatable.data.map(data => data.invested_amount).reduce((prev, next) => this.sumTwoItems(prev, next))
        - this.datatable.data.map(data => data.net_payout).reduce((prev, next) => this.sumTwoItems(prev, next)));
      },
      getTotalInvestedAmount() {
        return this.formatNumeric(this.datatable.data.map(data => data.invested_amount).reduce((prev, next) => this.sumTwoItems(prev, next)));
      },
      getTotalNetEarnings() {
        return this.formatNumeric(this.datatable.data.map(data => data.net_earnings).reduce((prev, next) => this.sumTwoItems(prev, next)));
      }
    },

    computed: {
      rebuildDatatableData() {
        let that = this;

        this.datatable.data.forEach(element => {
          element.top_up = that.formatNumeric(element.top_up);
          element.withdrawal = that.formatNumeric(element.withdrawal);
          element.incentives = that.formatNumeric(element.incentives);
          element.net_payout = that.formatNumeric(element.net_payout);
          element.invested_amount = that.formatNumeric(element.invested_amount);
          element.net_earnings = that.formatNumeric(element.net_earnings);
        });

        return this.datatable.data;
      },
    }
  }
</script>

<style scoped>
  .v-card__title, .v-card__subtitle {
    padding: 8px 12px; 
  }
</style>