<template>
    <div class="card">
      <div class="card-header">
        <h2>Datos de activación del plugin</h2>
      </div>
      <div class="card-body">
        <p>Token de activación: {{ tokenActivacion }}</p>
        <p>ID de Usuario: {{ id }}</p>
      </div>
    </div>
    <div class="card">
      <div class="card-header">
        <h2>Registro de mensajes</h2>
      </div>
      <div class="card-body">
        <v-row>
          <v-col cols="6">
            <h5>Mensajes Gastados</h5>
            <p>{{ user.spent_messages }}</p>
          </v-col>
          <v-col cols="6">
            <h5>Mensajes Faltantes</h5>
            <p :class="user.message_quantity < 3? 'cant_alert':'cant_calm'">{{ user.message_quantity }} </p>
          </v-col>
        </v-row>
      </div>
    </div>
    <div class="card">
      <div class="card-header">
        <h2>Estadísticas</h2>
      </div>
      <div class="card-body">
        <div class="row">
          <div class="col-md-6">
            <div class="card mb-6">
              <div class="card-header">Gráfico de barras</div>
              <div class="card-body">
                <canvas ref="barChart"></canvas>
              </div>
            </div>
          </div>
          <div class="col-md-6 col-lg-6">
            <div class="card mb-4">
              <div class="card-header">Gráfico de líneas</div>
              <div class="card-body">
                <canvas ref="lineChart"></canvas>
              </div>
            </div>
          </div>
          <div class="col-md-6 col-lg-12">
            <div class="card mb-4">
              <div class="card-header">Gráfico circular</div>
              <div class="card-body">
                <canvas ref="doughnutChart"></canvas>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import {VRow,VCol} from 'vuetify/lib/components';
  import { Chart, registerables } from 'chart.js';
  import axios from 'axios';
  Chart.register(...registerables);
  
  export default {
    data(){
      return{
        tokenActivacion: localStorage.getItem('authToken'),
        id: localStorage.getItem('user_id'),
        messages: [],
        transactions: [],
      }
    },
    components:{
        VRow,
        VCol
    },
    props: {
      user: {
        required: true,
        type: Array
      }
    },
    mounted() {
      this.getData();
    },
    methods: {
      createBarChart(transactions) {
        const countByMonth = {};

        transactions.forEach(transaction => {
          const date = new Date(transaction.created_at);
          const month = date.toLocaleString('default', { month: 'long', year: 'numeric' });
          if (countByMonth[month]) {
            countByMonth[month] += parseInt(transaction.cant_message);
          } else {
            countByMonth[month] = parseInt(transaction.cant_message);
          }
        });

        const labels = Object.keys(countByMonth);
        const data = Object.values(countByMonth);

        const ctx = this.$refs.barChart.getContext('2d');
        new Chart(ctx, {
          type: 'bar',
          data: {
            labels: labels,
            datasets: [
              {
                label: 'Cantidad de mensajes comprados',
                data: data,
                backgroundColor: '#2196f3',
              },
            ],
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
            scales: {
              y: {
                beginAtZero: true,
              },
            },
          },
        });
      },
      createLineChart(messages) {
        const countByMonth = {};

        messages.forEach(message => {
          const date = new Date(message.created_at);
          const month = date.toLocaleString('default', { month: 'long', year: 'numeric' });
          if (countByMonth[month]) {
            countByMonth[month] += 1;
          } else {
            countByMonth[month] = 1;
          }
        });

        const labels = Object.keys(countByMonth);
        const data = Object.values(countByMonth);

        const ctx = this.$refs.lineChart.getContext('2d');
        new Chart(ctx, {
          type: 'line',
          data: {
            labels: labels,
            datasets: [
              {
                label: 'Mensajes enviados',
                data: data,
                borderColor: '#2196f3',
                fill: false,
              },
            ],
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
            scales: {
              y: {
                beginAtZero: true,
              },
            },
          },
        });

      },
      createDoughnutChart() {
        const ctx = this.$refs.doughnutChart.getContext('2d');
        const data = [
          this.user.spent_messages?? 0,
          this.user.message_quantity ?? 100
        ]
        new Chart(ctx, {
          type: 'doughnut',
          data: {
            labels: ['Mensajes Gastados', 'Mensajes Disponibles'],
            datasets: [
              {
                data: data,
                backgroundColor: ['#ff5252', '#2196f3'],
              },
            ],
          },
          options: {
            responsive: true,
            maintainAspectRatio: false,
          },
        });
      },
      getData() {
          const urlBase = process.env.VUE_APP_API_URL;

          const config = {
              headers: {
              Authorization: 'Bearer ' + localStorage.getItem('accessToken'),
              }
          };

          const user_id = localStorage.getItem('user_id');

          axios
              .get(urlBase + 'api/global/' +user_id, config)
              .then(response => {

                  this.messages = response.data.messages;
                  this.createLineChart(this.messages);

                  this.transactions = response.data.transactions;
                  this.createBarChart(this.transactions);
                  
                  this.createDoughnutChart(); // Llamar al método para crear el gráfico después de que los datos se hayan cargado
                
              })
              .catch(error => {
              this.errorMessage =
                  'Se produjo un error al consultar datos. Por favor, inténtalo de nuevo.'; // Mostrar mensaje de error
              // Ocurrió un error al enviar la solicitud
              console.error(error); // Imprime el error en la consola
              // Puedes mostrar un mensaje de error aquí
              });
      },
    },
  };
  </script>
  
  <style scoped>
  .card {
    margin-top: 10px;
  }

  .cant_alert{
    text-decoration: underline;
    color: red;
  }

  .cant_calm{
    color: blue;
  }
  

  </style>
  