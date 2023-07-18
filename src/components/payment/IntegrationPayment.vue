<template>
    <div>
      <form ref="paymentForm"></form>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        apiKey: process.env.VUE_APP_APP_KEY_PAYCO,
        name: "Compra de paquetes de mensajes",
        description: "Sistema de asistencia inteligente",
        currency: "usd",
        country: "co",
        testMode: process.env.VUE_APP_APP_DEBUG,
        responseUrl: process.env.VUE_APP_APP_URL + "dashboard",
        confirmationUrl: process.env.VUE_APP_APP_CONFIRMATION_EPAYCO,
        methodConfirmation: "get",
      };
    },
    props: {
      cost: {
        type: Number,
        required: true
      }
    },
    mounted() {
      console.log(this.cost);
      this.loadCheckoutScript(this.cost);
    },
    methods: {
      loadCheckoutScript(cost) {
        const script = document.createElement("script");
        script.src = "https://checkout.epayco.co/checkout.js";
        script.className = "epayco-button";
        script.setAttribute("data-epayco-key", this.apiKey);
        script.setAttribute("data-epayco-amount", cost);
        script.setAttribute("data-epayco-name", this.name);
        script.setAttribute("data-epayco-description", this.description);
        script.setAttribute("data-epayco-currency", this.currency);
        script.setAttribute("data-epayco-country", this.country);
        script.setAttribute("data-epayco-test", this.testMode);
        script.setAttribute("data-epayco-external", "false");
        script.setAttribute("data-epayco-response", this.responseUrl);
        script.setAttribute("data-epayco-confirmation", this.confirmationUrl);
        script.setAttribute(
          "data-epayco-methodconfirmation",
          this.methodConfirmation
        );
  
        this.$refs.paymentForm.appendChild(script);
      },
    },
  };
</script>
  