<template>
    <div>
        <h1 class="display-4 text-center">Listado de Tareas</h1>
        <hr>
        <div class="row">
            <div class="col-lg-8 offset-lg-2">
                <div class="card mt-4">
                    <div class="card-body">
                        <div class="input-group">
                            <input type="text" v-model="tarea"
                                class="form-control form-control-lg" placeholder="Agregar Tarea">
                            <div class="input-group-append">
                                <button v-on:click="agregarTarea"
                                    class="btn btn-success btn-lg">Agregar</button>
                            </div>
                        </div>
                        <br />
                        <div class="text-center">
                            <div v-if="loading" class="spinner-border text-success" role="status">
                                 <span class="visually-hidden">Loading...</span>
                            </div>
                        </div>
                        <h5 v-if="listTarea.length == 0">No hay tareas para mostrar</h5>
                        <ul v-if="!loading" class="list-group">
                            <li v-for="(tarea, index) of listTarea" :key="index"
                                class="class list-group-item d-flex justify-content-between">
                                <span 
                                    v-on:click="editarTarea(tarea, tarea.idTarea)" 
                                    class="cursor"
                                    v-bind="{'text-success': tarea.estado}" >
                                    <i v-bind:class="[tarea.estado ?  'fa-solid fa-circle-check' : 'fa-regular fa-circle']"></i>
                                </span>
                                {{ tarea.nombre }}
                                <span class="text-danger cursor" v-on:click="eliminarTarea(tarea.idTarea)">
                                    <i class="fa-solid fa-trash"></i>
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


    export default {
        name: "ComponenteTareas",
        data(){
            return{
                tarea: "",
                listTarea: [],
                loading: false,
            }
        },
        methods: {
            agregarTarea() {
                const tarea = {
                    nombre: this.tarea,
                    estado: false
                }
                this.loading = true;
                axios.post("https://localhost:7056/api/Tarea/CrearTarea", tarea).then(response => {
                    console.log(response);
                    this.loading = false;
                    this.obtenerTareas();
                }).catch(error => {
                    console.log(error);
                    this.loading = false;
                })
                this.tarea = "";
            },
            eliminarTarea(id){
                this.loading = true;
                axios.delete(`https://localhost:7056/api/Tarea/Eliminar/${id}`).then(response => {
                    this.loading = false;
                    this.obtenerTareas();
                    console.log(response);
                }).catch(error => {
                    console.log(error);
                    this.loading = false;
                })
            },
            editarTarea(tarea, id){
                this.loading = true;
                axios.put(`https://localhost:7056/api/Tarea/Actualizar/${id}`, tarea).then(() => {
                    this.loading = false;
                    this.obtenerTareas();
                }).catch(() => this.loading = false); 
            },
            obtenerTareas(){
                this.loading = true;
                axios.get("https://localhost:7056/api/Tarea/GetAllTarea").then(response => {
                   
                    console.log(response);
                    this.listTarea = response.data;
                    this.loading = false;
                }).catch(() => this.loading = false);
            }
        },
        created: function(){
            this.obtenerTareas();
        }
    }
</script>

<style lang="scss" scoped>
.cursor {
    cursor: pointer;
}
</style>