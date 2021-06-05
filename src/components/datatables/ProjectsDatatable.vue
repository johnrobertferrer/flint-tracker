<template>
  <v-data-table
    dense
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
                    md="12"
                  >
                    <v-text-field
                      v-model="editedItem.name"
                      :rules="[ rules.required ]"
                      counter="50"
                      maxlength="50"
                      label="Project Name"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="3"
                  >
                    <v-text-field
                      v-model.number="formattedRate"
                      :rules="[ rules.required ]"
                      type="number"
                      label="Rate"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="3"
                  >
                    <v-text-field
                      v-model.number="formattedMonths"
                      :rules="[ rules.required ]"
                      type="number"
                      label="Months"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="6"
                  >
                    <v-select
                      :items="['Balloon', 'Equal']"
                      :rules="[ rules.required ]"
                      v-model="editedItem.repayment_type"
                      label="Repayment Type"
                    ></v-select>
                  </v-col>
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
                          label="Funded Date"
                          v-on="on"
                          @click:clear="editedItem.funded_date = null"
                        ></v-text-field>
                      </template>
                      <v-date-picker
                        v-model="editedItem.funded_date"
                        @change="datePicker = false"
                      ></v-date-picker>
                    </v-menu>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="3"
                  >
                    <v-text-field
                      v-model.number="formattedSellingPrice"
                      type="number"
                      label="Selling Price"
                    ></v-text-field>
                  </v-col>
                  <v-col
                    cols="12"
                    sm="6"
                    md="6"
                  >
                    <v-text-field
                      v-model="editedItem.link"
                      :rules="[ rules.counter ]"
                      counter="50"
                      maxlength="50"
                      label="Link"
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
    name: 'ProjectsDatatable',

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
          name: null,
          rate: null,
          months: null,
          repayment_type: null,
          selling_price: null,
          link: '',
          funded_date: null,
        },
        datePicker: false,
        defaultItem: {
          name: null,
          rate: null,
          months: null,
          repayment_type: null,
          selling_price: null,
          link: '',
          funded_date: null,
        },
        rules: {
          required: value => !!value || 'Required.',
          counter: value => value.length <= 50 || 'Max 50 characters',
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

      validate(data) {
        if (data.name === null || data.name === '') {
          return true;
        }

        if (data.months === null || data.months === '') {
          return true;
        }

        if (data.rate === null || data.rate === '') {
          return true;
        }

        return false;
      },

      save () {
        let callback = this.validate(this.editedItem);

        if (callback !== undefined && callback) {
          return false;
        }

        if (this.editedIndex > -1) {
          Object.assign(this.items[this.editedIndex], this.editedItem)
        } else {
          this.items.push(this.editedItem)
        }
        this.close()
      }
    },

    computed: {
      formTitle () {
        return this.editedIndex === -1 ? 'New Row' : 'Edit Row'
      },
      formattedDate () {
        return this.editedItem.funded_date ? moment(this.editedItem.funded_date).format('YYYY-MM-DD') : ''
      },
      formattedRate: {
        get () { return this.editedItem.rate },
        set (newVal) { this.editedItem.rate = newVal }
      },
      formattedMonths: {
        get () { return this.editedItem.months },
        set (newVal) { this.editedItem.months = newVal }
      },
      formattedSellingPrice: {
        get () { return this.editedItem.selling_price },
        set (newVal) { this.editedItem.selling_price = newVal }
      },
    }
  }
</script>

<style scoped>

</style>