<template>
  <v-data-table
    :headers="headers"
    :items="items"
    :search="search"
    :items-per-page="settings.items_per_page"
    class="elevation-1"
  >
    <template v-slot:top>
      <v-toolbar
        flat
      >
        <v-dialog
          v-model="dialog"
          max-width="700px"
        >
          <template v-slot:activator="{ on, attrs }">
            <v-btn
              color="#ff5722"
              dark
              class="mr-4"
              v-bind="attrs"
              v-on="on"
            >
              Insert New Row
            </v-btn>
          </template>
          <v-card>
            <v-card-title>
              <span class="text-h5">{{ formTitle }}</span>
            </v-card-title>

            <v-card-text>
              <v-container>
                <v-row>
                  <v-col
                    cols="12"
                    sm="6"
                    md="3"
                  >
                    <v-menu
                      v-model="datePicker"
                      :close-on-content-click="false"
                      max-width="290"
                    >
                      <template v-slot:activator="{ on, attrs }">
                        <v-text-field
                          :value="formattedDate"
                          clearable
                          readonly
                          v-bind="attrs"
                          label="Date"
                          v-on="on"
                          @click:clear="editedItem.date = null"
                        ></v-text-field>
                      </template>
                      <v-date-picker
                        v-model="editedItem.date"
                        @change="datePicker = false"
                      ></v-date-picker>
                    </v-menu>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="9"
                  >
                    <v-text-field
                      v-model="editedItem.details"
                      :rules="[ rules.counter ]"
                      counter="25"
                      maxlength="25"
                      label="Details"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="3"
                  >
                    <v-text-field
                      v-model.number="formattedTopUp"
                      type="number"
                      label="Top Up"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="3"
                  >
                    <v-text-field
                      v-model.number="formattedWithdrawal"
                      type="number"
                      label="Withdrawal"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="3"
                  >
                    <v-text-field
                      v-model.number="formattedIncentives"
                      type="number"
                      label="Incentives"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="3"
                  >
                    <v-text-field
                      v-model.number="formattedNetPayout"
                      type="number"
                      label="Net Payout"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="9"
                  >
                    <v-select
                      :items="getProjects()"
                      item-value="id"
                      item-text="name"
                      v-model="editedItem.project_id"
                      label="Project"
                    ></v-select>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="3"
                  >
                    <v-text-field
                      v-model.number="formattedInvestedAmount"
                      type="number"
                      label="Invested Amount"
                      :disabled="editedItem.project_id == null"
                    ></v-text-field>
                  </v-col>
                </v-row>
              </v-container>
            </v-card-text>

            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn
                color="blue darken-1"
                text
                @click="close"
              >
                Cancel
              </v-btn>
              <v-btn
                color="blue darken-1"
                text
                @click="save"
              >
                Save
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-dialog v-model="dialogDelete" max-width="500px">
          <v-card>
            <v-card-title class="text-h5">Are you sure you want to delete this row?</v-card-title>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn color="blue darken-1" text @click="closeDelete">Cancel</v-btn>
              <v-btn color="blue darken-1" text @click="deleteItemConfirm">OK</v-btn>
              <v-spacer></v-spacer>
            </v-card-actions>
          </v-card>
        </v-dialog>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Search"
          single-line
          hide-details
        ></v-text-field>
      </v-toolbar>
    </template>
    <template v-slot:item.actions="{ item }">
      <v-icon
        small
        class="mr-2"
        @click="editItem(item)"
      >
        mdi-pencil
      </v-icon>
      <v-icon
        small
        @click="deleteItem(item)"
      >
        mdi-delete
      </v-icon>
    </template>
    <template v-slot:no-data>
      <v-btn
        color="#ff5722"
        dark
        @click="initialize"
      >
        Refresh
      </v-btn>
    </template>
  </v-data-table>
</template>

<script>
  export default {
    name: 'TransactionsDatatable',

    props: [
      'data',
      'headers',
      'settings'
    ],

    data () {
      return {
        dialog: false,
        dialogDelete: false,
        search: '',
        editedIndex: -1,
        editedItem: {
          date: moment(new Date).format('YYYY-MM-DD'),
          details: '',
          top_up: null,
          withdrawal: null,
          incentives: null,
          net_payout: null,
          project_id: null,
          invested_amount: null,
          net_earnings: null
        },
        datePicker: false,
        defaultItem: {
          date: moment(new Date).format('YYYY-MM-DD'),
          details: '',
          top_up: null,
          withdrawal: null,
          incentives: null,
          net_payout: null,
          project_id: null,
          invested_amount: null,
          net_earnings: null
        },
        rules: {
          required: value => !!value || 'Required.',
          counter: value => value.length <= 25 || 'Max 25 characters',
        }
      }
    },

    watch: {
      dialog (val) {
        val || this.close()
      },
      dialogDelete (val) {
        val || this.closeDelete()
      },
    },

    created () {
      this.initialize();
    },

    methods: {
      initialize () {
        this.items = this.data;
      },

      editItem (item) {
        this.editedIndex = this.items.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialog = true
      },

      deleteItem (item) {
        this.editedIndex = this.items.indexOf(item)
        this.editedItem = Object.assign({}, item)
        this.dialogDelete = true
      },

      deleteItemConfirm () {
        this.items.splice(this.editedIndex, 1)
        this.closeDelete()
      },

      close () {
        this.dialog = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },

      closeDelete () {
        this.dialogDelete = false
        this.$nextTick(() => {
          this.editedItem = Object.assign({}, this.defaultItem)
          this.editedIndex = -1
        })
      },

      save () {
        if (this.editedIndex > -1) {
          Object.assign(this.items[this.editedIndex], this.editedItem)
        } else {
          this.items.push(this.editedItem)
        }
        this.close()
      },

      getProjects () {
        return [
          { id: 1, name: 'test 1'},
          { id: 2, name: 'test 2'},
          { id: 3, name: 'test 3'},
          { id: 4, name: 'test 4'},
          { id: 5, name: 'test 5'}
        ];
      }
    },

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Row' : 'Edit Row'
      },
      formattedDate () {
        return this.editedItem.date ? moment(this.editedItem.date).format('YYYY-MM-DD') : ''
      },
      formattedTopUp: {
        get () { return this.editedItem.top_up },
        set (newVal) { this.editedItem.top_up = newVal }
      },
      formattedWithdrawal: {
        get () { return this.editedItem.withdrawal },
        set (newVal) { this.editedItem.withdrawal = newVal }
      },
      formattedIncentives: {
        get () { return this.editedItem.incentives },
        set (newVal) { this.editedItem.incentives = newVal }
      },
      formattedNetPayout: {
        get () { return this.editedItem.net_payout },
        set (newVal) { this.editedItem.net_payout = newVal }
      },
      formattedRemainingPayout: {
        get () { return this.editedItem.remaining_payout },
        set (newVal) { this.editedItem.remaining_payout = newVal }
      },
      formattedInvestedAmount: {
        get () { return this.editedItem.invested_amount },
        set (newVal) { this.editedItem.invested_amount = newVal }
      },
      formattedNetEarnings: {
        get () { return this.editedItem.net_earnings },
        set (newVal) { this.editedItem.net_earnings = newVal }
      }
    }
  }
</script>

<style scoped>

</style>