<template>
    <div>
      <h2>Información del Plugin</h2>
      <p><strong>Nombre del Plugin:</strong> {{ plugin.name }}</p>
      <p><strong>Versión:</strong> {{ plugin.version }}</p>
      <p><strong>Autor: </strong><a href="https://carlosandresarevalo.com/"> {{ plugin.author }}</a></p>
      <p><strong>Última Fecha de Actualización:</strong> {{ plugin.lastUpdated }}</p>
      <a :href="plugin.zipUrl" download>
        <v-btn class="btn btn-primary">Descargar Archivo ZIP</v-btn>
      </a>
    </div>
</template>
  
<script>
  export default {
    data() {
      return {
        plugin: {
          name: 'SAI (Sistema de Asistencia Inteligente)',
          version: '1.0',
          author: 'Carlos Andres Arevalo Cortes',
          lastUpdated: '2023-07-14',
          zipUrl: 'https://drive.google.com/uc?export=download&id=12Imr_kBLmZW9LJsj0-UwPiMbsFNU1A6d'
        }
      };
    }
  };
</script>
  