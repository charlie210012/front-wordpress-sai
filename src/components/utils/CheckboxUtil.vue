<template>
  <v-card>
    <v-card-title>
      <h2>Plugins de Wordpress</h2>
    </v-card-title>
    <sub>Selecciona los plugins de los que esperas el asistente extraiga información <b>Pronto estará disponible</b></sub>
    <v-card-text>
      <v-row>
        <v-col v-for="texto in textos" :key="texto.id" cols="12" sm="6" md="4" lg="3">
          <v-checkbox v-model="texto.checked" :label="texto.texto"></v-checkbox>
        </v-col>
      </v-row>
    </v-card-text>
    <v-card-actions>
      <v-btn color="primary" @click="guardarCheckbox(textos)">Guardar</v-btn>
    </v-card-actions>
    <v-snackbar v-model="showSnackbar" :color="snackbarColor" top right>
      {{ snackbarMessage }}
    </v-snackbar>
  </v-card>
</template>

<script>
import { VCard, VCardTitle ,VCardText, VRow, VCol, VCheckbox, VCardActions, VBtn, VSnackbar } from 'vuetify/lib/components';
import axios from 'axios';
export default {
  components: {
    VCard,
    VCardTitle,
    VCardText,
    VRow,
    VCol,
    VCheckbox,
    VCardActions,
    VBtn,
    VSnackbar,
  },
  props: {
    textos: {
      type: Array,
      required: true,
    },
    id:{
      type: Number,
      required: true,
    }
  },
  data() {
    return {
      showSnackbar: false,
      snackbarMessage: '',
      snackbarColor: '',
    };
  },
  methods: {
    guardarCheckbox(textos) {
      this.saveItem(textos);
    },
    saveItem(textos) {
      const urlBase = process.env.VUE_APP_API_URL;
      axios.post(urlBase + 'api/data/' + this.id +'/update', {
        principle: { "plugins": textos },
        name: "otherPluginConexion",
      }, {
        headers: {
          Authorization: `Bearer ${localStorage.getItem("accessToken")}`,
        },
      })
      .then(response => {
        if (response.status === 200) {
          this.showSnackbar = true;
          this.snackbarMessage = 'Conexión de plugin realizada correctamente.';
          this.snackbarColor = 'success';
        }
      })
      .catch(error => {
        this.showSnackbar = true;
        this.snackbarMessage = 'Ocurrió un error al guardar la conexión de plugin.';
        this.snackbarColor = 'error';
        console.error(error);
      });
    },
  },
};
</script>

<style>
</style>
