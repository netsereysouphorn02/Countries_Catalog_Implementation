<template>
  <v-app>
    <h1>Country Catalog</h1>
    <v-card class="main-card">
      <v-card-title>
        <v-text-field
          v-model="search"
          append-icon="mdi-magnify"
          label="Search Country Name"
          single-line
          hide-details
        ></v-text-field>
      </v-card-title>
        <v-btn text @click="sortByCountryName('asc')">ASC (A-Z) ▲</v-btn>
        <v-btn text @click="sortByCountryName('desc')">DESC (Z-A) ▼</v-btn>
    </v-card>

    <table class="elevation-2 head">
      <thead>
        <tr>
          <th style="width:40px">Image</th>
          <th> Country Name</th>
          <th>2 character Country Code</th>
          <th>3 character Country Code</th>
          <th>Native Country Name</th>
          <th>Alternative Country Name</th>
          <th>Country Calling Codes</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in paginatedCountries" :key="index">
          <td>
            <img :src="item.flags.png" :alt="item.name.official" />
          </td>
          <td>
            <FullInfo :item="item"></FullInfo>
          </td>
          <td>{{ item.cca2 }}</td>
          <td>{{ item.cca3 }}</td>
          <td>{{ item.name.nativeName }}</td>
          <td>{{ Object.values(item.altSpellings).join(', ') }}</td>
          <td>{{ item.idd.suffixes }}</td>
        </tr>
      </tbody>
    </table>

    <div class="pagination">
      <button @click="prevPage" :disabled="currentPage === 1">Previous</button>
      <span>Page {{ currentPage }} of {{ totalPages }}</span>
      <button @click="nextPage" :disabled="currentPage === totalPages">Next</button>
    </div>
  </v-app>
</template>

<script>
import axios from 'axios';
import fuzzysort from 'fuzzysort';
import FullInfo from '../components/FullInfo.vue';

export default {
  components: {
    FullInfo,
  },
  data() {
    return {
      search: '',
      tableLoading: true,
      countries: [],
      currentPage: 1,
      itemsPerPage: 25,
      sortOrder: 'asc', // Default sorting order
    };
  },
  created() {
    this.getAllCountries();
  },
  computed: {
    filteredCountries() {
      if (!this.search) return this.countries;

      return fuzzysort.go(this.search, this.countries, {
        key: 'name.official',
      }).map((result) => result.obj);
    },
    sortedCountries() {
      return this.filteredCountries.slice().sort((a, b) => {
        const nameA = a.name.official.toLowerCase();
        const nameB = b.name.official.toLowerCase();
        return this.sortOrder === 'asc' ? nameA.localeCompare(nameB) : nameB.localeCompare(nameA);
      });
    },
    totalPages() {
      return Math.ceil(this.sortedCountries.length / this.itemsPerPage);
    },
    paginatedCountries() {
      const startIndex = (this.currentPage - 1) * this.itemsPerPage;
      const endIndex = startIndex + this.itemsPerPage;
      return this.sortedCountries.slice(startIndex, endIndex);
    },
  },
  methods: {
    async getAllCountries() {
      try {
        const res = await axios.get('https://restcountries.com/v3.1/all');
        this.countries = res.data;
        this.tableLoading = false;
      } catch (err) {
        console.log(err);
      }
    },
    prevPage() {
      if (this.currentPage > 1) {
        this.currentPage -= 1;
      }
    },
    nextPage() {
      if (this.currentPage < this.totalPages) {
        this.currentPage += 1;
      }
    },
    sortByCountryName(order) {
      this.sortOrder = order;
    },
  },
};
</script>

<style scoped>
.main-card {
  margin: 10px;
  padding: 10px;
  background-color: #f5f5f5;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.elevation-2 {
  border-collapse: collapse;
  width: 100%;
}

.elevation-2 th,
.elevation-2 td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

.pagination {
  margin-top: 10px;
  display: flex;
  justify-content: center;
}
</style>
