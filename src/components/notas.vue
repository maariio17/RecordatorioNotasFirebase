<template lang="html">

  <section class="notas">
    <div class="box">
      <form>
        <input type="text" v-on:keyup.enter="anadirElemento" v-on:keyup="teclaPulsada" v-model="input">
        <input type="button" v-bind:disabled="isButtonDisabled" v-on:click="anadirElemento" value="Añadir a la Lista">
      </form>
    </div>
    <div class="cuerpo">
      <p>Filtrar tareas: <input v-on:keyup.enter="anadirElemento" v-on:keyup="teclaPulsada" v-model="empiezaPor"></p>

      <p>{{tareasPendientes}} tareas pendientes de un total de {{totalTareas}}</p>
      <button v-on:click="borrarTareasCompletadas"><i class="fas fa-trash"></i>Borrar tareas completadas</button>

      <ol>
      <!-- Hacer que el campo que se muestra aquí sea el filtrado, que es calculado -->
        <li v-for="(todo, index) in listaFiltrada" v-bind:key="todo+index" v-bind:class="{ completado: todo.completado}">
          <div class="listadoNotas">{{ todo.titulo }} <img src="Imagenes/edit.png"> <img v-on:click="cambiarEstadoTarea(index)" src="Imagenes/checked.png"> </div>
          <div class="datosNotas">
            <p class="prioridad">Prioridad {{todo.prioridad}}</p>
          </div>
        </li>
      </ol>
    </div>
    
  </section>

</template>

<script lang="js">
import {db} from "../db.js"
  export default  {
    name: 'notas',
    props: [],
    mounted () {
      if (localStorage.listaTareas) {
        this.todos = JSON.parse(localStorage.listaTareas);
      }
    },
    data () {
      return {
        input: "",
        todos: [],
        listaNotas: [],
        isButtonDisabled: true,
        empiezaPor: ""
        //tareaCompletada: false
      }
    },
    methods: {
      anadirElemento: function () {
        if (this.input.length > 0) {
          this.todos.push({
            titulo: this.input,
            prioridad: 0,
            fechaCreacion: new Date(),
            completado: false
          });
        this.input = "";
        this.actualizarLocalStorage();
        }
      },
      cambiarEstadoTarea: function (posicion) {
        this.todos[posicion].completado = !this.todos[posicion].completado;
        this.actualizarLocalStorage();
        /*if(this.todos[posicion].completado){
          this.tareaCompletada = true;
        } else{
          this.tareaCompletada = false;
        }*/
      },
      borrarTareasCompletadas: function () {
        this.todos = this.todos.filter(todo => !todo.completado);
        this.actualizarLocalStorage();
      },
      teclaPulsada: function () {
        if (this.input.length > 0) {
          this.isButtonDisabled = false;
        } else {
          this.isButtonDisabled = true;
        }
      }
    },
    computed: {
      totalTareas: function (){
        return this.todos.length;
      },
      tareasCompletadas: function () {
        return this.todos.filter(todo => todo.completado).length;
      },
      tareasPendientes: function () {
        return this.todos.filter(todo => !todo.completado).length;
      },
      listaFiltrada: function () {
        /* return todo.titulo.startsWith(this.empiezaPor); */
        if (this.empiezaPor == "") {
          console.log("vacio");
          return this.listaNotas;
        }
        let listaFiltrada = this.listaNotas.filter(nota => {
          if (nota.titulo.indexOf(this.empiezaPor) >= 0) {
            console.log("hola");
            return true;
          } else {
            conselo.log("adios");
            return false;
          }
        });

        // Ordenar
        //Ordenar por Titulo
        listaFiltrada = listaFiltrada.sort( (todo1, todo2) => {
          if (todo1.titulo < todo2.titulo) {
            return -1;
          } else if (todo1.titulo > todo2.titulo) {
            return 1;
          } else {
            return 0;
          }
        });

        return listaFiltrada;
      }
    },
    firestore: {
      listaNotas: db.collection('notas')
    }
  }


</script>

<style scoped>
  .notas{
    text-align: center;
  }

  .listadoNotas img:nth-child(1){
    margin-left: 20px;
  }

  .listaNotas{
    background: grey;
    border-radius: 50%;
  }

  .datosNotas{
    display: inline-flex;
  }

  .datosNotas p{
    margin: 5px;
    border-radius: 10px;
    padding: 5px;
  }

  .prioridad{
    background: red;
    
  }

  .fecha{
    background: yellow;
  }

  /*.completado{
    background: green;
  }*/
  
  .cuerpo{
    position: relative;
    margin: 10px;
    float: left;
    left: 30%;
    text-align: center;
  }


  .cuerpo input{
    font-size: 20px;
    box-sizing: border-box;
    transition: .5s;
    width: 20vw;
  }

  .cuerpo ol{
    text-align: left;
  }


  .box {
    /*position: relative;*/
    /*top: 10%;
    left: 50%;
    transform: translate(-50%,-50%);*/
    margin: 10px;
  }
  .box input{
    /*position: relative;*/
    display: inline-block;
    font-size: 20px;
    box-sizing: border-box;
    transition: .5s;
  }

  .box input[type="text"]{
    background: #fff;
    width: 30vw;
    height: 50px;
    border: none;
    outline: none;
    padding: 0 25px;
    border-radius: 25px 0 0 25px;
  }
  .box input[type="button"]{
    left: -5px;
    /*position: relative;*/
    border-radius: 0 25px 25px 0;
    width: 10vw;
    min-width: 10rem;
    height: 50px;
    border: none;
    outline: none;
    cursor: pointer;
    background: #ffc107;
    color: #fff;
  }
  .box input[type="button"]:hover{
    background: green;
  }
</style>
