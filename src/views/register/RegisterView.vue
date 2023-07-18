<template>
    <v-container class="register-container">
      <v-row justify="center">
        <v-col cols="12" sm="8" md="5">
          <v-card class="register-card elevation-10">
            <v-card-title class="text-center">
              <h2 class="register-title">Registro de Usuario</h2>
            </v-card-title>
            <v-card-text>
              <v-alert
                v-if="error"
                :type="typeError"
                dismissible
              >
                {{ errorMessage }}
              </v-alert>
              <v-form @submit.prevent="register">
                <v-text-field
                  v-model="name"
                  label="Nombre"
                  outlined
                  required
                ></v-text-field>
                <v-text-field
                  v-model="email"
                  label="Correo"
                  outlined
                  required
                  type="email"
                ></v-text-field>
                <v-text-field
                  v-model="password"
                  label="Contraseña"
                  outlined
                  required
                  type="password"
                ></v-text-field>
                <v-btn color="primary" type="submit" block>Registrarse</v-btn>
              </v-form>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
      <div class="text-center mt-4">
        <p>¿Tienes cuenta? <router-link to="/login">Iniciar Sesion</router-link></p>
      </div>
    </v-container>
  </template>
  
  <script>
  import {VAlert, VContainer, VRow, VCol, VCard, VCardText, VForm, VBtn, VCardTitle, VTextField } from "vuetify/lib/components";
  import axios from 'axios';

  export default {
    data() {
      return {
        name: '',
        email: '',
        password: '',
        errorMessage: '',
        error: false,
        typeError: '',
      };
    },
    components: {
      VContainer,
      VRow,
      VCol,
      VCard,
      VCardText,
      VForm,
      VBtn,
      VCardTitle,
      VTextField,
      VAlert,
    },
    methods: {
      register() {
    // Crear un objeto con los datos del usuario
        const newUser = {
          name: this.name,
          email: this.email,
          password: this.password
        };

        // Obtener el Bearer Token de autenticación
        const token = localStorage.getItem("accessToken"); // Reemplazar con tu Bearer Token

        // Configurar el encabezado de autorización con el Bearer Token
        const config = {
          headers: {
            Authorization: `Bearer ${token}`
          }
        };

        const urlBase = process.env.VUE_APP_API_URL;

        // Realizar la petición POST para registrar el usuario con el encabezado de autorización
        axios.post(urlBase + 'api/user', newUser, config)
          .then(response => {
            // La petición fue exitosa, realizar las acciones necesarias
            if(response.status === 201){
              this.error = true;
              this.typeError = 'success';
              this.errorMessage = 'Usuario registrado con exito!'
              localStorage.setItem("authToken", response.data.token);
              localStorage.setItem("user_id", response.data.user.id);
              setTimeout(() =>{
                this.$router.push('/dashboard');
              },500);
            }else{
              this.error = true;
              this.typeError = 'error';
              this.errorMessage = response.data.errors
            }
            // Reiniciar los campos de entrada
            this.name = '';
            this.email = '';
            this.password = '';
            setTimeout(() =>{
                this.error = false;
              },2000);
          })
          .catch(error => {
            // Hubo un error en la petición, mostrar mensaje de error
            console.error('Error al registrar usuario:', error);
          });
      },
    },
    created() {
        const token = localStorage.getItem('authToken');
        const id = localStorage.getItem('user_id');
        if (token) {
        this.$router.push('/dashboard/'+id);
        }
    },
  };
  </script>
  
  <style>
  .register-container {
    margin-top: 100px;
  }
  
  .register-card {
    background-color: #ffffff;
    color: #000000;
  }
  
  .register-title {
    color: #000000;
  }
  </style>
  