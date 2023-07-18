<template>
    <v-card class="mx-auto" max-width="800">
      <v-card-title class="text-center">Información de Usuario</v-card-title>
      <v-card-text>
        <v-row>
          <v-col cols="12">
            <v-list>
              <v-list-item>
                <v-list-item-content>
                  <v-list-item-title class="font-weight-bold">Nombre:</v-list-item-title>
                  <v-list-item-subtitle>{{ user.name }}</v-list-item-subtitle>
                </v-list-item-content>
              </v-list-item>
              <v-list-item>
                <v-list-item-content>
                  <v-list-item-title class="font-weight-bold">Correo Electrónico:</v-list-item-title>
                  <v-list-item-subtitle>{{ user.email }}</v-list-item-subtitle>
                </v-list-item-content>
              </v-list-item>
              <v-list-item>
                <v-list-item-content>
                  <v-list-item-title class="font-weight-bold">Token de Activación:</v-list-item-title>
                  <v-list-item-subtitle>{{ tokenActivacion }}</v-list-item-subtitle>
                </v-list-item-content>
              </v-list-item>
            </v-list>
          </v-col>
        </v-row>
      </v-card-text>
    </v-card>
  </template>
  
  <script>
  import { VCard, VCardTitle, VCardText, VRow, VCol, VList, VListItem, VListItemContent, VListItemTitle, VListItemSubtitle } from 'vuetify/lib/components';
  
  export default {
    components: {
      VCard,
      VCardTitle,
      VCardText,
      VRow,
      VCol,
      VList,
      VListItem,
      VListItemContent,
      VListItemTitle,
      VListItemSubtitle,
    },
    data() {
      return {
        tokenActivacion: localStorage.getItem('authToken'),
      };
    },
    props: {
      user: {
        required: true,
        type: Array
      }
    }

  };
  </script>
  
  <style>
  </style>
  