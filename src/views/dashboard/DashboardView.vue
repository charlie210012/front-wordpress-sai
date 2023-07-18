<template>
    <v-app>
      <alert-payment v-if="startModal" @click-event = "hadleEvent"></alert-payment>
      <button-payment></button-payment>
      <v-navigation-drawer v-model="drawer" app>
        <v-avatar size="64" style="margin-top: 50px;">
          <img src="./assets/SaiCloud.svg" alt="Imagen de perfil" style="width: 64px; height: 64px;">
        </v-avatar>
        <v-list>
          <v-list-item-group v-model="activeItem">
            <v-list-item @click="navigateTo('StatisticsComponent')">
              <v-list-item-icon>
                <v-icon>mdi-view-dashboard</v-icon>
              </v-list-item-icon>
              <v-list-item-title>Dashboard</v-list-item-title>
            </v-list-item>
            <v-list-item @click="navigateTo('PluginComponent')">
              <v-list-item-icon>
                <v-icon>mdi-puzzle</v-icon>
              </v-list-item-icon>
              <v-list-item-title>Plugin</v-list-item-title>
            </v-list-item>
            <v-list-item @click="navigateTo('HomeComponent')">
              <v-list-item-icon>
                <v-icon>mdi-cog</v-icon>
              </v-list-item-icon>
              <v-list-item-title>Configuración</v-list-item-title>
            </v-list-item>
            <v-list-item @click="navigateTo('UserComponent')">
              <v-list-item-icon>
                <v-icon>mdi-account</v-icon>
              </v-list-item-icon>
              <v-list-item-title>Perfil</v-list-item-title>
            </v-list-item>
            <v-list-item @click="navigateTo('ContactComponent')">
              <v-list-item-icon>
                <v-icon>mdi-phone</v-icon>
              </v-list-item-icon>
              <v-list-item-title>Contacto</v-list-item-title>
            </v-list-item>
          </v-list-item-group>
        </v-list>

      </v-navigation-drawer>
      <v-app-bar app>
        <v-app-bar-nav-icon @click.stop="drawer = !drawer"></v-app-bar-nav-icon>
        <v-toolbar-title>Dashboard</v-toolbar-title>
        <v-spacer></v-spacer>
        <v-toolbar-title>{{user.name}}</v-toolbar-title>
        <v-btn text @click="logout">Salir</v-btn>
      </v-app-bar>
      <v-main>
        <v-container>
            <component :user="user" :is="component" />
        </v-container>
      </v-main>
    </v-app>
  </template>
  
  <script>
  import { VIcon,VApp, VNavigationDrawer, VList, VListItemGroup, VListItem, VListItemIcon, VListItemTitle, VAppBar, VAppBarNavIcon, VToolbarTitle, VSpacer, VBtn, VMain, VContainer, VAvatar } from 'vuetify/lib/components';
  import HomeComponent from '../../components/home/HomeComponent.vue';
  import UserComponent from '../../components/user/UserComponent.vue';
  import StatisticsComponent from '../../components/statistics/StatisticsComponent.vue';
  import ContactComponent from '../../components/contact/ContactComponent.vue';
  import PluginComponent from '../../components/plugin/PluginComponent.vue';
  import ButtonPayment from '../../components/payment/ButtonPayment.vue';
  import AlertPayment from '../../components/utils/AlertPayment.vue';
  import axios from "axios";
  import moment from 'moment';
  export default {
    components: {
      VApp,
      VNavigationDrawer,
      VList,
      VListItemGroup,
      VListItem,
      VListItemIcon,
      VListItemTitle,
      VAppBar,
      VAppBarNavIcon,
      VToolbarTitle,
      VSpacer,
      VBtn,
      VMain,
      VContainer,
      VIcon,
      VAvatar,
      HomeComponent,
      UserComponent,
      StatisticsComponent,
      PluginComponent,
      ButtonPayment,
      AlertPayment,
      ContactComponent,
    },
    data() {
      return {
        drawer: true,
        startModal: false,
        component: 'StatisticsComponent',
        user:{},
      };
    },
    methods: {
      navigateTo(component) {
          this.component = component;
      },
      logout() {
          // Realiza las acciones necesarias para cerrar la sesión del usuario
          // Por ejemplo, puedes borrar el token de acceso almacenado en el local storage
          localStorage.removeItem('juser_id');
          localStorage.removeItem('authToken');
          // Redirecciona al usuario a la página de inicio de sesión
          this.$router.push('/login');
      },
      getDataUser() {
          const urlBase = process.env.VUE_APP_API_URL;

          const config = {
              headers: {
              Authorization: 'Bearer ' + localStorage.getItem('accessToken'),
              }
          };

          const user_id = localStorage.getItem('user_id');

          axios
              .get(urlBase + 'api/user/' +user_id, config)
              .then(response => {

                  this.user = response.data.user;
                  console.log(this.user);
              })
              .catch(error => {
              this.errorMessage =
                  'Se produjo un error al consultar datos. Por favor, inténtalo de nuevo.'; // Mostrar mensaje de error
              // Ocurrió un error al enviar la solicitud
              console.error(error); // Imprime el error en la consola
              // Puedes mostrar un mensaje de error aquí
              });
      },
      hadleEvent(){
        this.$router.push({ path: '/dashboard' });
        setTimeout(() => {
          window.location.reload();
        }, 500);
      },
      getTransacion(){
        const urlBase = "https://secure.epayco.co/validation/v1/reference/";

        axios
          .get(urlBase + this.refPayco)
          .then(response => {
            console.log(response.data.data);
            // Parsea la fecha utilizando Moment.js
            let date = moment(response.data.data.x_fecha_transaccion, 'YYYY-MM-DD HH:mm:ss');

            // Suma 6 meses a la fecha
            let newDate = date.add(6, 'months');

            // Obtén la nueva fecha en el formato deseado
            let finalDate = newDate.format('YYYY-MM-DD HH:mm:ss');
            const data = {
              transaction_id: response.data.data.x_ref_payco,
              code_transaction_id: this.refPayco,
              description: response.data.data.x_description,
              amount: response.data.data.x_amount,
              status: response.data.data.x_response_reason_text == "Aprobada"?1:0,
              cant_message: response.data.data.x_amount * 70,
              user_id: localStorage.getItem("user_id"),
              payment_date: response.data.data.x_fecha_transaccion,
              expiration_date: finalDate,
              payer_email: response.data.data.x_customer_email,
              data: JSON.stringify(response.data.data)
            }

            this.registerTransaccion(data);
            
          })
          .catch(error => {
            this.errorMessage =
              'Se produjo un error al consultar datos. Por favor, inténtalo de nuevo.'; // Mostrar mensaje de error
            // Ocurrió un error al enviar la solicitud
            console.error(error); // Imprime el error en la consola
            // Puedes mostrar un mensaje de error aquí
          });
      },
      async registerTransaccion(data) {
        const urlBase = process.env.VUE_APP_API_URL;
        const id = localStorage.getItem('user_id');
        const requestBody = data;

        const config = {
          headers: {
            Authorization: 'Bearer '+ localStorage.getItem('accessToken')
          }
        };

        axios.post(urlBase + 'api/payment/'+id, requestBody, config)
          .then(response => {
            if(response.status  === 201){
              this.startModal = true;
            }

            // Realizar acciones después de la solicitud exitosa
          })
          .catch(error => {
            console.error('Error:', error);
            // Realizar acciones en caso de error
          });

        this.modalOpen = false;
      },
    },
    created() {
      this.refPayco = this.$route.query.ref_payco;
    },
    mounted(){
        this.getDataUser();
        if(this.refPayco != null) {
          this.getTransacion();
        }
    }
  };
  </script>
  
  <style scoped>

  </style>
  