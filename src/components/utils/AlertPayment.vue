<template>
    <v-dialog v-model="showModal" max-width="500px">
      <v-card>
        <v-card-title class="headline">¡Felicidades!</v-card-title>
        <v-card-text>
          <p>Tu nuevo paquete de mensajes ha sido cargado con éxito.</p>
        </v-card-text>
        <v-card-actions>
          <v-btn color="primary" text @click="closeModal">Cerrar</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </template>
  
  <script>
  import { VDialog, VCard, VCardTitle, VCardText, VCardActions, VBtn } from 'vuetify/lib/components';
  
  export default {
    components: {
      VDialog,
      VCard,
      VCardTitle,
      VCardText,
      VCardActions,
      VBtn
    },
    data() {
      return {
        showModal: true,
      };
    },
    methods: {
      openModal() {
        this.showModal = true;
      },
      closeModal() {
        this.showModal = false;
        this.$emit('click-event');

      }
    }
  };
  </script>
  
  <style scoped>
  .v-dialog {
    border-radius: 8px;
  }
  
  .v-card {
    background-color: #ffffff;
    box-shadow: 0 2px 10px rgba(0, 0, 0, 0.15);
  }
  
  .v-card-title {
    background-color: #1c64f2;
    color: #ffffff;
    padding: 24px;
    border-top-left-radius: 8px;
    border-top-right-radius: 8px;
  }
  
  .v-card-text {
    padding: 24px;
  }
  
  .v-card-actions {
    justify-content: flex-end;
    padding: 24px;
  }
  </style>
  