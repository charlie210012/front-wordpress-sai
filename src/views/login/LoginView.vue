<template>
    <v-container class="login-container">
      <v-row justify="center">
        <v-col cols="12" sm="8" md="5">
          <v-card class="elevation-10">
            <v-card-title class="text-center">
              <h2>Iniciar Sesión</h2>
            </v-card-title>
            <v-card-text>
              <v-alert
                v-if="error"
                :type="typeError"
                dismissible
              >
                {{ errorMessage }}
              </v-alert>
              <v-form @submit.prevent="login">
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
                <v-btn color="primary" type="submit" block>Iniciar sesión</v-btn>
              </v-form>
            </v-card-text>
          </v-card>
        </v-col>
      </v-row>
      <div class="text-center mt-4">
        <p>¿No tienes una cuenta? <router-link to="/register">Registrarme</router-link></p>
      </div>
    </v-container>
  </template>
  
  <script>
  import {VAlert, VContainer,VRow,VCol,VCard,VCardText,VForm,VBtn,VCardTitle,VTextField} from "vuetify/lib/components";
  import axios from "axios";
  export default {
    data() {
      return {
        email: '',
        password: '',
        errorMessage: '',
        error: false,
        typeError: '',
      };
    },
    components:{
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
        login() {
        const urlBase = process.env.VUE_APP_API_URL;
        try {
            // Realiza la autenticación y obtén el token de acceso aquí
            // Por ejemplo, utilizando Axios para hacer una solicitud HTTP POST a la API
            axios.post(urlBase + 'api/login', {
              email: this.email, // Utiliza this.email en lugar de email
              password: this.password,
                }, {
                headers: {
                    Authorization: `Bearer ${localStorage.getItem("accessToken")}`,
                },
            })
            .then((response) => {
                    //Si la autenticación es exitosa, guarda el token en el almacenamiento local y redirige al usuario
                if (response.status == 200) {
                    localStorage.setItem("authToken", response.data.token);
                    localStorage.setItem("user_id", response.data.user.id);
                    setTimeout(() =>{
                      this.$router.push('/dashboard');
                    },200);
                } else {
                    alert("Error: la autenticación falló");
                // Si la autenticación falla, muestra un mensaje de error
                }
            })
            .catch((error) => {
                if (error.response.status === 401) {
                    this.errorMessage = 'credenciales incorrectas.'
                // Mostrar alerta de error específica para el estado 401
                this.typeError = 'error';
                this.error = true;
                setTimeout(() => {
                    this.error = false;
                    this.errorMessage = '';
                }, 2000);
                } else {
                // Mostrar alerta genérica para otros errores
                    this.errorMessage = 'ocurrió un error en la autenticación.'
                    this.typeError = 'error';
                    this.error = true;
                    setTimeout(() => {
                        this.error = false;
                        this.errorMessage = '';
                    }, 2000);
                }
            });
          } catch (error) {
              this.error = true;
          }
        },
    },
    created() {
        const token = localStorage.getItem('authToken');
        if (token) {
        this.$router.push('/dashboard');
        }
    },
  };
  </script>
  
  <style scoped>
  .login-container {
    margin-top: 100px;
    
  }


  </style>
  