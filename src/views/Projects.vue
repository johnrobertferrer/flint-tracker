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
    </v-row>
  </v-container>
</template>

<script>
  import ProjectsDatatable from '../components/datatables/ProjectsDatatable.vue';

  export default {
    name: 'Projects',

    components: {
      'datatable': ProjectsDatatable
    },

    data() {
      return {
        datatable: {
          headers: [
            { text: 'Name', value: 'name' },
            { text: 'Rate', value: 'rate' },
            { text: 'Months', value: 'months' },
            { text: 'Repayment Type', value: 'repayment_type' },
            { text: 'Funded Date', value: 'funded_date' },
            { text: 'Selling Price', value: 'selling_price' },
            { text: 'Link', value: 'link', width: '100px' },
            { text: 'Actions', value: 'actions', sortable: false },
          ],
          data: [
            {
              name: 'Candon Fire Station 2',
              rate: 12,
              months: 6,
              repayment_type: 'Equal',
              funded_date: '2021-03-20',
              selling_price: 5000000,
              link: 'https://www.flint.com.ph/platform/listing/candon-city-fire-station-2-43',
            },
            {
              name: 'Candon Fire Station 3',
              rate: 13,
              months: 12,
              repayment_type: 'Balloon',
              funded_date: '2021-04-22',
              selling_price: 3000000,
              link: 'https://www.flint.com.ph/platform/listing/candon-city-fire-station-2-43',
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
      }
    },

    computed: {
      rebuildDatatableData() {
        let that = this;

        this.datatable.data.forEach(element => {
          element.selling_price = that.formatNumeric(element.selling_price);
        });

        return this.datatable.data;
      },
    }
  }
</script>

<style scoped>
</style>