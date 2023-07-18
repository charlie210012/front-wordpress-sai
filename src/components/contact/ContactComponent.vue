<template>
    <div class="container">
      <h2>Datos de contacto</h2>
      <div class="row" style="margin-top: 30px;">
        <div class="card col-md-6">
            <p>Página web personal: <a :href="website" target="_blank">{{ website }}</a></p>
            <p>Página web de la empresa: <a :href="websiteBussine" target="_blank">{{ website }}</a></p>
            <p>Teléfono: {{ phone }}</p>
            <p>Correo electrónico personal: {{ email }}</p>
            <p>Correo electrónico de la empresa: {{ emailBussines }}</p>
        </div>
        <div class="col-md-6">
          <a :href="whatsappURL" target="_blank" class="btn btn-primary">Contactar por WhatsApp</a>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    data() {
      return {
        website: 'https://carlosandresarevalo.com/',
        websiteBussine: 'https://sidevtech.com/',
        phone: '(602) 4045698',
        email: 'carlosandresarev@yahoo.com.co',
        emailBussines: 'admin@sidevtech.com',
        mobile: "3177864344",
      };
    },
    computed: {
      whatsappURL() {
        const phoneNumber = this.mobile.replace(/\D/g, ''); // Eliminar caracteres no numéricos del número de teléfono
        return `https://wa.me/${phoneNumber}`;
      },
    },
  };
  </script>
  
  <style>
  .card{
    padding: 10px;
  }
  </style>
  