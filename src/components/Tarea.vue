<template>
  <div>
    <h1 class="display-4 text-center">Listado de Tareas</h1>
    <hr />
    <div class="row">
      <div class="col-lg-8 offset-lg-2">
        <div class="card mt-4">
          <div class="card-body">
            <div class="input-group">
              <input type="text" v-model="tarea" class="form-control form-control-lg" placeholder="Agregar Tarea " />
              <div class="input-group-append">
                <button v-on:click="agregarTarea()" class="btn btn-success btn-lg">Agregar</button>
              </div>
            </div>
            <br />

            <div class="text-center">
              <div v-if="loading" class="spinner-border text-success" role="status">
                <span class="sr-only">Loading...</span>
              </div>
            </div>

            <h5 v-if="listTareas.length == 0">No hay tareas para realizar</h5>
            
            <ul v-if="!loading" class="list-group">
              <li v-for="(tarea, index) of listTareas" :key="index" class="list-group-item d-flex justify-content-between">
                <span class="cursor" v-bind:class="{ 'text-success': tarea.activo }" v-on:click="editarTarea(tarea, tarea.idTarea, index)">
                  <i v-bind:class="[tarea.activo ? 'far fa-circle' : 'fas fa-check-circle',]"></i>
                </span>
                {{ tarea.nombreTarea }}
                <span class="text-danger cursor" v-on:click="eliminarTarea(tarea.idTarea, index)">
                  <i class="fas fa-trash-alt"></i>
                </span>
              </li>
            </ul>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

//const URL = "";
export default {
  name: "Tarea",
  data() {
    return {
      tarea: "",
      listTareas: [],
      loading: false,
    };
  },
  methods: {
    agregarTarea() {
      const tarea = {
        CodTarea: "TA" + (this.listTareas.length + 1),
        NombreTarea: this.tarea,
        DescTarea: this.tarea,
        IdCategoria: 1,
        Activo: true,
      };
      this.listTareas.push(tarea);
      this.loading = true;
      axios
        .post("http://localhost:5172/api/Tarea/GuardarTareas", tarea)
        .then((response) => {
          console.log(response);
          this.loading = false;
          this.obtenerTareas();
        })
        .catch((error) => {
          console.error(error);
          this.loading = false;
        });
      this.tarea = "";
    },
    eliminarTarea(idTarea, index) {
      this.listTareas.splice(index, 1)
      this.loading = true;
      axios
        .delete("http://localhost:5172/api/Tarea/" + idTarea)
        .then((response) => {
          console.log(response);
          this.obtenerTareas();
          this.loading = false;
        })
        .catch((error) => {
          console.log(error);
          this.loading = false;
        });
    },
    editarTarea(tarea, idTarea, index) {
      this.listTareas[index].activo = !tarea.activo
     //this.loading = true;
     /*axios.put("" + idTarea, tarea).then(()=> {
       this.obtenerTareas();
       this.loading = false
     }).catch(() => this.loading = false);*/
    },
    obtenerTareas() {
      this.loading = true;
      axios.get("http://localhost:5172/api/Tarea/GetTareas")
      .then((response) => {
        //console.log(response.data.tareas);
        this.listTareas = response.data.tareas;
        this.loading = false;
      }).catch(() => this.loading = false);
    },
  },
  created: function () {
    this.obtenerTareas();
  },
};
</script>

<style scoped>
.cursor {
  cursor: pointer;
}
</style>