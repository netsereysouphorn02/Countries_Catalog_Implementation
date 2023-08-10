<template>
  <v-app>
    <v-card class="main-card">
      <v-card-title>
        <v-text-field
          v-model="searchQuery"
          append-icon="mdi-magnify"
          label="Search Country Name"
          single-line
          hide-details
          @input="search"
        ></v-text-field>
      </v-card-title>
        <v-btn text @click="sortByCountryName('asc')">ASC (A-Z) ▲</v-btn>
        <v-btn text @click="sortByCountryName('desc')">DESC (Z-A) ▼</v-btn>
    </v-card>
    <div class="table-container">
      <table class="elevation-2 head">
        <thead>
          <tr>
            <th style="width:40px">Image</th>
            <th style="width:50px"> Country Name</th>
            <th>2 character Country Code</th>
            <th>3 character Country Code</th>
            <th>Native Country Name</th>
            <th>Alternative Country Name</th>
            <th>Country Calling Codes</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="(item, index) in searchResuts" :key="index">
            <td>
              <img :src="item.flags.png" :alt="item.name.official" class="small-image" />
            </td>
            <td class="c_name">
              <FullInfo :item="item"></FullInfo>
            </td>
            <td>{{ item.cca2 }}</td>
            <td>{{ item.cca3 }}</td>
            <td><pre>{{ JSON.stringify(item.name.nativeName, null, 2) }}</pre></td>
            <td>{{ Object.values(item.altSpellings).join(', ') }}</td>
            <td><pre>{{ JSON.stringify(item.idd, null, 2) }}</pre></td>
          </tr>
        </tbody>
      </table>
    </div>

    <div class="pagination">
            <v-btn
              small
              color="primary"
              dark
              @click="prevPage"
              :disabled="currentPage === 1"
            >
              Previous
            </v-btn>
           <p>Page {{ currentPage }} of {{ totalPages }}</p>
            <v-btn
              small
              color="primary"
              dark
              @click="nextPage"
              :disabled="currentPage === totalPages"
            >
              Next
            </v-btn>

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
  //declear value and can access in this file
  data() {
    return {
      tableLoading: true,
      countries: [],
      currentPage: 1,
      itemsPerPage: 25,
      sortOrder: 'asc', // Default sorting order
      searchQuery: '',
    };
  },
  created() {
    this.getAllCountries();
  },
  computed: {
    filteredCountries() {
      // if (!this.search) 
      return this.countries;

      // return fuzzysort.go(this.search, this.countries, {
      //   key: 'name.official',
      // }).map((result) => result.obj);
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
    // searchResults() {
    //   if (this.searchQuery) {
    //     return this.paginatedCountries.filter(country => {
    //       return country.name.official.toLowerCase().includes(this.searchQuery.toLowerCase());
    //     });
    //   } else {
    //     return this.paginatedCountries;
    //   }
    // },
    searchResuts() {
      if (this.searchQuery) {
        return this.countries.filter(country =>
          country.name.official.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
      } else {
        return this.paginatedCountries;
      }
    },
    // searchResuts(){
    //   if (this.search) {
    //     const sortedProducts = this.countries.filter(country => country.name.official.toString() === this.search);
    //     return sortedProducts;
    //   }

    //   return this.paginatedCountries;
    // }
    
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
    search() {
      this.currentPage = 1; 
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
  .table-container {
    overflow-x: auto;
    max-width: 100%;
    margin-bottom: 20px;
  }

.pagination {
  margin:  30px 0px;
  display: flex;
  justify-content: space-evenly;
}
.c_name{
  display: flex;
  justify-content: center;
  text-align: center;
}
.small-image {
  max-width: 100px; 
  max-height: 100px;
}

</style>
