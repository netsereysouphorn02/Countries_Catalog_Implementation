<template>
  <div class="app">
    <h1>Country Catalog</h1>
    <input v-model="searchQuery" placeholder="Search by Country Name" />
    <select v-model="sortBy">
      <option value="asc">Sort A-Z</option>
      <option value="desc">Sort Z-A</option>
    </select>
    <CountryCatalog />
  </div>
</template>

<script>
import CountryCatalog from "../components/CountryCatalog.vue";

export default {
  components: {
    CountryCatalog,
  },
  data() {
    return {
      searchQuery: "",
      sortBy: "asc",
    };
  },
};
</script>

<style>
.app {
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}
input {
  margin-bottom: 10px;
  padding: 5px;
}
select {
  margin-left: 10px;
}
</style>
