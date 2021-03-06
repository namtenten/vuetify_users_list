<template>
  <v-container>
    <v-row class="text-center">
      <v-col cols="12" v-if="false">
        <v-img
          :src="require('../../assets/logo/logo_256.svg')"
          class="my-3"
          contain
          height="200"
        />
      </v-col>

      <v-col
        class="mb-1"
        cols="12"
      >
        <h2 class="headline font-weight-bold mb-0">
          USERS LIST | RESTful
        </h2>

        <v-row>
          <UserEditDialog :formTitle="this.formTitle" :users_list="users_list" :dialog="dialog" :selectedStatus="users_list.editedItem.status" />
        </v-row>

        <v-row>
          <v-col
            class="mb-1"
            cols="12"
          >
            <div class="text-right">
              <!-- https://materialdesignicons.com/ -->
              <v-btn color="orange accent-4" fab dark class="mb-0 mr-1" @click="fetchAPIData()"><v-icon color="white">mdi-refresh</v-icon></v-btn>
              <v-btn color="orange accent-4" fab dark class="mb-0 mr-1" @click="showAddDialog()"><v-icon color="white">mdi-account-multiple-plus</v-icon></v-btn>
            </div>

          </v-col>
        </v-row>

        <v-row justify="center">
          <v-data-table
            v-model="users_list.selected"
            :headers="users_list.headers"
            :items="users_list.items"
            class="elevation-1"
            :item-class="itemRowClass"
            :multi-sort="true"
            :items-per-page="10"
            item-key="id"
            :show-select="false"
            :search="users_list.search"
          >

            <template v-slot:top="{ pagination, options, updateOptions }">
              <v-data-footer 
                :pagination="pagination" 
                :options="options"
                @update:options="updateOptions"
                items-per-page-text="$vuetify.dataTable.itemsPerPageText"/>
            </template>

            <template slot="item" slot-scope="props">
              <tr :class="itemRowClass(props.item)">
                <td>
                  <v-layout justify-left>
                    <a @click="showEditDialog(props.item)">
                        <v-icon small color="orange accent-4">mdi-pencil</v-icon>
                        {{ props.item.name }}
                    </a>
                  </v-layout>
                </td>
                <td>
                  <v-layout justify-left>
                    <a :href="'mailto:' + props.item.email">{{ props.item.email }}</a>
                  </v-layout>
                </td>
                <td>
                  <v-layout justify-center>
                    {{ props.item.status.text }}
                  </v-layout>
                </td>
                <td>
                  <v-btn icon class="mx-0" @click="showEditDialog(props.item)">
                      <v-icon small color="orange accent-4">mdi-pencil</v-icon>
                  </v-btn>

                  <v-btn icon class="mx-0" @click="showDeleteDialog(props.item)">
                    <v-icon small color="red">mdi-delete</v-icon>
                  </v-btn>
                </td>
              </tr>
            </template>

          </v-data-table>

        </v-row>
      </v-col>
    </v-row>
  </v-container>
</template>

<script>
  // https://www.codeply.com/p/m0nip2TdUv

  // import Vue from "vue";
  import UserEditDialog from "@/components/UserEditDialog"
  import Vue from 'vue'
  import axios from 'axios'

  // import Vuex from 'vuex'
  import { mapGetters } from 'vuex'
  // import { mapActions } from "vuex"

  Vue.use(axios)

  export default {
    name: 'UsersList'

    ,components: {
      UserEditDialog,
    }

    ,data: () => ({
      dialog: false
      , users_list: {
        api: {url:'http://localhost:5000/api/users'},
        search: '',
        selected: [],
        headers: [
          // https://vuetifyjs.com/en/styles/colors/
          { text: '名前', value: 'name', sortable: true, class: "orange accent-4 white--text title" },
          { text: 'email', value: 'email', sortable: true, class: "orange accent-4 white--text title" },
          { text: '状態', value: 'status.text', sortable: true, class: "orange accent-4 white--text title" },
          { text: '', value: 'actions', sortable: false, class: "orange accent-4 white--text title" }
        ],
        items: []
        ,editedIndex: -1
        ,editedItem: {id: null,  name: null, email:null, status:{value:'valid', text:'有効'}}
        ,defaultItem: {id: null,  name: null, email:null, status:{value:'valid', text:'有効'}}
      }
      , status_list : {
          default: {value:'valid', text: '有効'}
          , items: [
            {value:'valid', text: '有効'},
            {value:'invalid', text: '無効'},
            {value:'deleted', text: '削除'},
          ]
        }
    })

    , mounted () {
      // this.fetchAPIData()
    }
    , created () {
    }

    ,computed: {
      ...mapGetters({ 
        tableItems: 'tableItems'
      })

      , formTitle() {
        return ((this.users_list.editedIndex === -1)) ? 'New Item' : 'Edit Item'
      }
    }

    ,methods: {
      initialise() {
      }
      ,itemRowClass: function (item) {
         return (item.status.value == "deleted") ? 'grey--text text--lighten-1' : (item.status.value == "invalid" ? 'deep-orange--text text--darken-1' : 'green--text')
      }

      , fetchAPIData() { 
        axios
          .get(this.users_list.api.url)
          .then(response => {
            this.users_list.items = response.data
          })
      }

      ,showAddDialog:function() {
          this.users_list.editedIndex = -1
          this.users_list.editedItem = {...this.users_list.defaultItem}
          this.dialog = true

      }

      ,showEditDialog:function(item) {
          this.users_list.editedIndex = this.users_list.items.indexOf(item)
          this.users_list.editedItem = item
          this.dialog = true

      }

      ,showDeleteDialog:function(item) {
        const index = this.users_list.items.indexOf(item)
        if (index > -1) {
          this.$confirm(
            {
              title: `CONFIRM DIALOG`,
              message: `Are you sure you want to delete this item?`,
              button: {
                no: 'No',
                yes: 'Yes'
              },
              callback: confirm => {
                if (confirm) {
                  axios
                    .delete(this.users_list.api.url + "/" + item.id)
                    .then(() => {

                      this.users_list.items.splice(index, 1)

                      this.$notify({
                        group: 'app',
                        title: 'User deleted',
                        text: 'User information was deleted!'
                        , type: 'error'
                      });

                    })
                }
              }
            }
          )

        }

      }

    }
  }
</script>
