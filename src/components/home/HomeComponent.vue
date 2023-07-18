<template class="container-component">
    <div class="container">
      <div class="alert alert-info" role="alert">
        El sistema de asistencia inteligente se conectará automáticamente a los siguientes plugins si los tienes instalados en tu proyecto WordPress,
        si deseas que configuremos algun otro plugin para tu proyecto contactanos.
      </div>

      <ul class="list-group list-group-horizontal">
        <li class="list-group-item">WooCommerce</li>
      </ul>
    </div>
    <div class="card">
      <div class="card-header">
        <div class = "header-table">
          <h2>Principios del asistente inteligente</h2>
          <button class="btn btn-primary" @click="showDialog = true">Añadir</button>
        </div>
        
        <div class="form-group mt-3">
          <input type="text" class="form-control" v-model="searchText" placeholder="Buscar texto">
        </div>
      </div>
      <div class="card-body">
        <table class="table table-bordered">
          <thead>
            <tr>
              <th>Principio</th>
              <th>Acciones</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in filteredTextos" :key="item.id">
              <td>{{ item.principle }}</td>
              <td>
                <button class="btn btn-primary btn-sm" @click="editItem(item)">Editar</button>
                <button class="btn btn-danger btn-sm" @click="deleteItem(item.id)">Eliminar</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
      <div class="modal" :class="{ 'd-block': showDialog }">
        <div class="modal-dialog modal-dialog-centered">
          <div class="modal-content">
            <div class="modal-header">
              <h3 class="modal-title">Añadir Texto</h3>
              <button type="button" class="btn-close" @click="showDialog = false"></button>
            </div>
            <div class="modal-body">
              <form @submit.prevent="actionItem">
                <div class="mb-3">
                  <label for="texto" class="form-label">Nuevo Principio</label>
                  <input type="text" class="form-control" id="texto" v-model="principle" required>
                </div>
                <div class="modal-footer">
                  <button type="submit" class="btn btn-primary">Añadir</button>
                  <button type="button" class="btn btn-secondary" @click="showDialog = false">Cancelar</button>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import axios from "axios";
  export default {
    data() {
      return {
        principles: [],
        action: 'save',
        item: null,
        checkboxes: [], 
        showDialog: false,
        idCheckbox: null,
        principle: '',
        searchText: '',
      };
    },
    computed: {
      filteredTextos() {
        if (this.searchText) {
          return this.principles.filter((item) =>
            item.principle.toLowerCase().includes(this.searchText.toLowerCase())
          );
        }
        return this.principles;
      },
    },
    methods: {
      saveItem() {
        this.action = 'save';
        const urlBase = process.env.VUE_APP_API_URL;
        const id = localStorage.getItem('user_id');
        axios.post(urlBase + 'api/data/' + id, {
              principle: {"principle":this.principle}, // Utiliza this.email en lugar de email
              name: "vuePlugin",
                }, {
                headers: {
                    Authorization: `Bearer ${localStorage.getItem("accessToken")}`,
                },
            })
            .then((response) => {
                    //Si la autenticación es exitosa, guarda el token en el almacenamiento local y redirige al usuario
                if (response.status == 201) {
                  this.principle = '';
                  this.showDialog = false;
                  this.getItems();
                }
                // } else {
                   
                // }
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
      },
      getItems(){
        const urlBase = process.env.VUE_APP_API_URL;
        const id = localStorage.getItem('user_id');
        const config = {
            headers: {
            Authorization: 'Bearer ' + localStorage.getItem('accessToken'),
            }
        };
        axios.get(urlBase + 'api/data/' +id, config)
                .then(response => {

                    this.principles = response.data.principles;
                })
                .catch(error => {
                this.errorMessage =
                    'Se produjo un error al consultar datos. Por favor, inténtalo de nuevo.'; // Mostrar mensaje de error
                // Ocurrió un error al enviar la solicitud
                console.error(error); // Imprime el error en la consola
                // Puedes mostrar un mensaje de error aquí
                });
      },
      deleteItem(id) {
        const urlBase = process.env.VUE_APP_API_URL;
        axios.post(urlBase + 'api/data/' + id +'/delete',null, {
                headers: {
                    Authorization: `Bearer ${localStorage.getItem("accessToken")}`,
                },
            })
            .then((response) => {
                    //Si la autenticación es exitosa, guarda el token en el almacenamiento local y redirige al usuario
                console.log(response.data);
                this.getItems();
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
      },
      editItem(item) {
        this.action = 'edit';
        this.principle = item.principle;
        this.item = item.id;
        this.showDialog = true;
      },
      updateItem(){
        const urlBase = process.env.VUE_APP_API_URL;
        axios.post(urlBase + 'api/data/' + this.item +'/update',{
          principle: {"principle":this.principle},
        }, {
                headers: {  
                    Authorization: `Bearer ${localStorage.getItem("accessToken")}`,
                },
            })
            .then((response) => {
              console.log(response.data);
                    //Si la autenticación es exitosa, guarda el token en el almacenamiento local y redirige al usuario
                this.showDialog = false;
                this.action = 'save';
                this.getItems();
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
      },
      actionItem(){
        if(this.action === 'save'){
          this.saveItem();
        }else{
          this.updateItem();
        }
      }
    },
    mounted(){
      this.getItems();
    }
  };
  </script>
  
  <style scoped>

  .card {
    margin-top: 20px;
  }
  
  .modal {
    background-color: rgba(0, 0, 0, 0.5);
  }

  .header-table{
    display: flex;
    justify-content: space-between;
    margin: 10px;
  }

  .list-group {
  margin-top: 20px;
  display: flex;
  flex-wrap: nowrap;
}

.list-group-item {
  flex: 1;
}
  

  </style>
  