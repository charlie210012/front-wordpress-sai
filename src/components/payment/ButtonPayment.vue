<template>
    <div>
      <button class="floating-button position" @click="openModal">
        <b>Comprar mensajes</b>
      </button>
    </div>
    <div v-if="showConfirmation" class="modal-overlay">
      <div class="modal-content" v-if="!payment">
        <h3>Realizar Pago</h3>
        <form @submit.prevent="equivalenceUsd">
          <div class="form-group">
            <label for="messageQuantity" class="input-label">Cantidad de mensajes:</label>
            <br>
            <input type="text" id="messageQuantity" v-model="messageQuantity" required class="input-field" @input="filterNonNumeric">
          </div>
          <hr>
          <div class="form-group">
            <label for="dollarEquivalent" class="input-label">Equivalencia en dólares (USD):</label>
            <br>
            <input type="text" id="dollarEquivalent" v-model="dollarEquivalent" readonly class="input-field">
          </div>
          <p style="color: red;" v-if="showWarning" class="warning-message">
                El valor mínimo debe ser de 10 dolares.
            </p>
          <div class="modal-buttons">
            <button class="btn btn-secondary" @click="cancelPublish">Cancelar</button>
            <button v-if="!paym" class="btn btn-primary" @click = "clickPayment">
              Calcular
            </button>
            <Button style="margin-left: 10px;" v-if="paym">
                <IntegrationPayment :cost = "valueCost" />
            </Button>
          </div>
          
        </form>
      </div>
    </div>
  </template>
  
  <script>
  import IntegrationPayment from './IntegrationPayment.vue';
  
  export default {
    data() {
      return {
        payment: false,
        position: "left",
        showConfirmation: false,
        messageQuantity: null,
        dollarEquivalent: 0,
        valueCost: 0,
        paym: false,
        showWarning: false, // Variable para mostrar la advertencia
      };
    },
    watch: {
        messageQuantity() {
        this.paym = false;
      },
    },
    methods: {
      openModal() {
        this.showConfirmation = true;
      },
      cancelPublish() {
        this.showConfirmation = false;
      },
        clickPayment() {
            this.dollarEquivalent = this.messageQuantity / 50;
            if (this.dollarEquivalent < 10) {
                this.showWarning = true; // Mostrar advertencia si es menor a 10 dólares
            } else {
                this.paym = true;
                this.valueCost = this.dollarEquivalent;
                this.showWarning = false; // Ocultar advertencia si es mayor o igual a 10 dólares
            }
        },
      filterNonNumeric() {
        this.messageQuantity = this.messageQuantity.replace(/\D/g, '');
        },
    },
    components: {
      IntegrationPayment
    },
  };
  </script>
  
  <style scoped>
  .floating-button {
    position: fixed;
    bottom: 20px;
    background-color: #ce191c;
    color: #fff;
    padding: 10px;
    width: 100px;
    height: 100px;
    border-radius: 50%;
    cursor: pointer;
    transition: left 0.3s;
    border: none;
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    font-size: 14px;
    line-height: 1;
    z-index: 9999;
  }
  
  .position {
    left: 90%;
    top: 30%;
  }
  
  .modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 9999;
  }
  
  .modal-content {
    background-color: #fff;
    padding: 20px;
    max-width: 400px;
  }
  
  .modal-buttons {
    display: flex;
    justify-content: flex-end;
    margin-top: 20px;
  }
  
  .btn {
    margin-left: 10px;
  }
  
  .input-label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
  }
  
  .input-field {
    padding: 8px;
    width: 100%;
    border: 1px solid #ccc;
    border-radius: 4px;
    transition: border-color 0.3s;
  }
  
  .input-field:focus {
    outline: none;
    border-color: #1c64f2;
  }
  </style>
  