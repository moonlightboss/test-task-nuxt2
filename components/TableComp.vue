<template>
  <div>
    <search-comp :search.sync="search" />
  <v-data-table
    :headers="headers"
    :items="items"
    :search="search"
    :loading="loading"
    :footer-props="footerProps"
    :item-key="itemKey"
    :show-select="true"
    :single-select="false"
    @update:show-select="updateSelected"
    @input="updateSelection"
  >

    <template #[`item.actions`]="{ item }">
      <v-icon small @click="deleteItem(item)">mdi-delete</v-icon>
    </template>
    <template #[`item.date_of_birth`]="{ item }">
      {{ item.date_of_birth }}
    </template>
    <template #footer>
      <v-btn icon color="error" @click="deleteSelected">
        <v-icon>mdi-delete</v-icon>
      </v-btn>
    </template>
    </v-data-table>
  </div>
</template>

<script>
import axios from 'axios';
import SearchComp from './SearchComp.vue';

export default {
  components:{
    SearchComp
  },
  data() {
    return {
      headers: [
        { text: 'Name', value: 'name' },
        { text: 'Email', value: 'email' },
        { text: 'Date of Birth', value: 'date_of_birth' },
        { text: 'Actions', value: 'actions', sortable: false }
      ],
      items: [],
      search: '',
      loading: false,
      footerProps: {
        itemsPerPageText: 'Элементов на странице:',
        itemsPerPageOptions: [10, 20, 30],
        showCurrentPage: true
      },
      itemKey: 'name',
      selectAll: false
    }
  },
  computed: {
    filteredItems() {
      const query = this.search.toLowerCase();
      return this.items.filter(item => item.name.toLowerCase().includes(query));
    }
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    deleteItem(item) {
      const index = this.items.indexOf(item)
      if (index !== -1) {
        this.items.splice(index, 1)
      }
    },
    deleteSelected() {
      const selectedItems = this.items.filter(item => item._isSelected)
      selectedItems.forEach(item => {
        const index = this.items.indexOf(item)
        if (index !== -1) {
          this.items.splice(index, 1)
        }
      })
    },
    updateSelected(value) {
      this.selectAll = value;
      this.items.forEach(item => {
        item._isSelected = value;
      })
    },
    updateSelection(selectedItems) {
      this.items.forEach(item => {
        item._isSelected = selectedItems.includes(item);
      })
    },
    fetchData() {
      this.loading = true;
      axios
        .get('https://randomuser.me/api/?results=15')
        .then(response => {
          // Проверяем, что ответ сервера успешный и содержит данные
          if (response.status === 200 && response.data.results) {
            this.items = response.data.results.map(user => ({
              name: `${user.name.first} ${user.name.last}`,
              email: user.email,
              date_of_birth: user.dob.date,
              _isSelected: false
            }));
          } else {
            // В случае ошибки выводим сообщение
            console.error('Ошибка при получении данных:', response.statusText);
          }
          this.loading = false;
        })
        .catch(error => {
          console.error('Ошибка при получении данных:', error);
          this.loading = false;
        });
    }
  },
}
</script>
