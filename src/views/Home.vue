<template>
  <div class="home">
    <v-row>
      <v-col cols="12">
        <h1 class="my-5 text-center">Dolar a peso chileno App</h1>
        <v-card>
          <v-date-picker
            header-color="blue darken-1"
            color="green lighten-1"
            v-model="date"
            full-width
            locale="es-ec"
            :min="min"
            :max="max"
            @change="setDolarValue(dateFormated)"
          >
          </v-date-picker>
        </v-card>
        <v-card color="green darken-1" dark>
          <v-card-text class="display-1 text-center"> {{ valor }}</v-card-text>
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
// @ is an alias to /src

import axios from "axios";
import { mapMutations } from "vuex";

export default {
  name: "Home",
  data() {
    return {
      date: this.getActualDate(),
      min: "1984",
      max: this.getActualDate(),
      valor: null,
    };
  },
  methods: {
    ...mapMutations(["showLoader", "hiddenLoader"]),
    async setDolarValue(dateSelected) {
      try {
        this.showLoader({
          title: "Accediendo a la información",
          color: "secondary",
        });
        const datos = await axios(
          `https://mindicador.cl/api/dolar/${dateSelected}`
        );
        this.valor =
          datos.data.serie.length > 0
            ? datos.data.serie[0].valor
            : "Sin resultados";
      } catch (e) {
        console.log(e.message);
      } finally {
        console.log("API consumida");
        this.hiddenLoader();
      }
    },
    getActualDate() {
      return new Date().toISOString().substr(0, 10);
    },
  },
  computed: {
    dateFormated() {
      return this.date.split("-").reverse().join("-");
    },
  },

  created() {
    this.setDolarValue(this.dateFormated);
  },
};
</script>

