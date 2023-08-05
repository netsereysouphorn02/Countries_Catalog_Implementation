<template>
  <v-dialog v-model="dialog" persistent>
    <template v-slot:activator="{ on, attrs }">
      <v-btn
        :style="{ width: '150px' }"
        color="#E53935"
        class="ml-1"
        dark
        v-bind="attrs"
        v-on="on"
        @click="disableButton($event)"
      >
        {{ item.name.official }}
      </v-btn>
    </template>
    <v-card min-height="200">
      <v-container class="d-flex justify-right flex-column" style="text-align: left;">
        <div class="align-right">
          <h1>{{ item.name.official }}</h1>
          <p>Flag: <br> <img :src="item.flags.png" :alt="item.name.official" /></p>
          <p>CCN3: {{ item.ccn3 }}</p>
          <p>Region: {{ item.region }}</p>
          <p>Subregion: {{ item.subregion }}</p>
          <p>Languages: {{ Object.values(item.languages).join(', ') }}</p>
          <p>Currencies: {{ Object.keys(item.currencies).join(', ') }}</p>
          <p>Translations: {{ Object.keys(item.translations).join(', ') }}</p>
          <p>Borders: {{ item.borders.join(', ') }}</p>
          <p>timezones: {{ item.timezones.join(', ') }}</p>
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
      target: [],
    };
  },
  watch: {
    dialog(val) {
      if (!val) {
        this.target.style.visibility = '';
      }
    },
  },
  methods: {
    disableButton(event) {
      this.target = event.currentTarget;
      event.currentTarget.style.visibility = 'hidden';
    },
  },
};
</script>

<style scoped>
/* Add your custom styles here */
</style>
