<template>
  <div>
    <v-data-table
      :headers="headers"
      :items="filteredCountries"
      :search="search"
      :loading="tableLoading"
      class="elevation-2 head"
    >
      <template v-slot:top>
        <v-card-title style="padding-top: 0">
          <v-spacer></v-spacer>
          <v-text-field
            v-model="search"
            append-icon="mdi-magnify"
            label="Search"
            single-line
            hide-details
          ></v-text-field>
        </v-card-title>
      </template>
      <template v-slot:[`item.index`]="{ index }">
        <div class="body">{{ index + 1 }}</div>
      </template>
      <template v-slot:[`item.flags`]="{ item }">
        <div class="p-2 image">
        <img :src="item.flags.png" :alt="item.name.official" />
        </div>
      </template>
      <template v-slot:[`item.name.official`]="{ item }">
        <div class="p-2">
          <div ><FullInfo :item="item" ></FullInfo></div>
        </div>
      </template>
      <template v-slot:[`item.cca2`]="{ item }">
        <div class="p-2">
          <div>{{ item.cca2 }}</div>
        </div>
      </template>
      <template v-slot:[`item.cca3`]="{ item }">
        <div class="p-2">
          <div>{{ item.cca3 }}</div>
        </div>
      </template>
      <template v-slot:[`item.name.nativeName`]="{ item }">
        <div class="p-2">
          <div>{{ item.name.nativeName }}</div>
        </div>
      </template>
      <template v-slot:[`item.altSpellings`]="{ item }">
        <div class="p-2">
          <div>{{ item.altSpellings }}</div>
        </div>
      </template>

      <template v-slot:[`item.idd`]="{ item }">
        <div class="p-2">
          <div>{{ item.idd.suffixes }}</div>
          
        </div>
      </template>

    </v-data-table>
  </div>
</template>

<script>
import axios from 'axios';
import FullInfo from '../components/FullInfo.vue'
import fuzzysort from "fuzzysort";
export default {
    components: { 
      FullInfo,
  },
  data() {
    return {
      search: "",
      tableLoading: true,
    headers: [
    {
        text: "No",
        value: "index",
        align: "right",
       
    },
    { text: "Image", value: "flags", sortable: false },
    { text: "Country Name", value: "name.official" },
    { text: "2 character Country Code", value: "cca2" },
    { text: "3 character Country Code", value: "cca3" },
    { text: "Native Country Name", value: "name.nativeName" },
    { text: "Alternative Country Name", value: "altSpellings" },
    { text: "Country Calling Codes", value: "idd" },

    ],

      countries: [],
    };
  },
  

  created() {
    this.getAllCountries();
  },
  computed: {
    filteredCountries() {
      return this.countries.filter((country) =>
        country.name.official.toLowerCase().includes(this.search.toLowerCase())
      );
    }
  },
  methods: {
    async getAllCountries() {
      try {
        const res = await axios.get('https://restcountries.com/v3.1/all');
        this.countries = res.data;
        console.log(this.countries)
        this.tableLoading = false;
      } catch (err) {
        console.log(err);
      }
    },
  }
};
</script>

<style scoped>
  .body{
    text-align: right;
    padding-right: 30px;
  }


</style>