<template>
  <v-dialog v-model="dialog" persistent>
    <template v-slot:activator="{ on, attrs }">
      <v-btn
        small
        color="#E53935"
        class="ml-1 small-button"
        dark
        v-bind="attrs"
        v-on="on"
        @click="disableButton($event)"
      >
        {{ item.name.official }}
      </v-btn>
    </template>
    <v-card >
    <v-container class="d-flex justify-right flex-column" style="text-align: left;">
      <div class="align-right">
        <h1>{{ item.name.official }}</h1>
        <p>Flag: <br> <img :src="item.flags.png" :alt="item.name.official" /></p>
        <p>CCN3: {{ item.cca3 }}</p>
        <p>Region: {{ item.region }}</p>
        <p>Subregion: {{ item.subregion }}</p>
        <p>Languages: {{ Object.values(item.languages).join(', ') }}</p>
        <p>Currencies: {{ Object.keys(item.currencies).join(', ') }}</p>
        <p>Translations: {{ Object.keys(item.translations).join(', ') }}</p>
        <p v-if="item.borders && item.borders.length > 0">Borders: {{ item.borders.join(', ') }}</p>
        <p v-else>No bordering countries</p>
        <p>Timezones: {{ item.timezones.join(', ') }}</p>
      </div>
    </v-container>
      <v-container class="d-flex justify-center flex-column align-center">
        <div class="d-flex mt-4">
          <v-btn
            elevation="1"
            style="color: #00BFFF; border: 1px solid #00BFFF"
            text
            @click="dialog = false"
            width="80%"
          >
            Cancel
          </v-btn>
        </div>
      </v-container>
    </v-card>
  </v-dialog>
</template>

<script>
export default {
  props: ['item'],
  data() {
    return {
      dialog: false,
      loading: false,
      target: null,
    };
  },
  methods: {
    showDetails() {
      this.dialog = true;
    },
  },
};
</script>

<style scoped>
  .small-button {
    font-size: 10px;
    padding: 2px 6px; 
  }
</style>
