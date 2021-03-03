<template lang="html">

  <section class="lista-notas">
    <div class="box">
      <form>
        <input type="text" v-on:keyup.enter="anadirElemento" v-on:keyup="teclaPulsada" v-model="input">
        <input type="button" v-bind:disabled="isButtonDisabled" v-on:click="anadirElemento" value="Añadir a la Lista">
      </form>
    </div>
    <div class="cuerpo">
      <p>Filtrar tareas: <input v-on:keyup.enter="anadirElemento" v-on:keyup="teclaPulsada" v-model="empiezaPor"></p>

      <p>{{tareasPendientes}} tareas pendientes de un total de {{this.listaNotas.length}}</p>
    
      <ol>
      <!-- Hacer que el campo que se muestra aquí sea el filtrado, que es calculado -->
        <li v-for="(notas, index) in listaFiltrada" v-bind:key="notas.titulo">
          <div class="listadoNotas">{{ notas.titulo }} <img src="Imagenes/edit.png"> <img v-on:click="cambiarEstadoTarea(index)" src="Imagenes/checked.png"><img v-on:click="borrarTarea(index)" src="Imagenes/papelera.png"> </div>
          <div class="datosNotas">
            <p class="prioridad">Prioridad {{notas.prioridad}}</p>
            <p class="estado">Completado: {{notas.completado}}</p>
          </div>
        </li>
      </ol>
    </div>
  </section>

</template>

<script lang="js">
import {db} from "../db.js"
  export default  {
    name: 'lista-notas',
    props: [],
    mounted () {
    },
    data () {
      return {
        input: "",
        listaNotas: [],
        empiezaPor: "",
        isButtonDisabled: true,
      }
    },
    methods: {
      anadirElemento: function () {
        if (this.input.length > 0) {
          db.collection('notas').add({
            titulo: this.input,
            prioridad: 0,
            fechaCreacion: new Date(),
            completado: false
          });
        this.input = "";
        }
      },
      teclaPulsada: function () {
        if (this.input.length > 0) {
          this.isButtonDisabled = false;
        } else {
          this.isButtonDisabled = true;
        }
      },
      borrarTarea: function (posicion) {
        db.collection('notas').doc(this.listaNotas[posicion].id).delete();
      },
      cambiarEstadoTarea: function (posicion) {
        this.listaNotas[posicion].completado = !this.listaNotas[posicion].completado;
        db.collection('notas').doc(this.listaNotas[posicion].id).update({
          completado: this.listaNotas[posicion].completado
        });
      },
    },
    computed: {
      listaFiltrada: function () {
        if (this.empiezaPor) {
          return this.listaNotas.filter(nota => {
            return nota.titulo.indexOf(this.empiezaPor) != -1;
          });
        } else {
          return this.listaNotas;
        }
      },
      tareasPendientes: function () {
        return this.listaNotas.filter(nota => !nota.completado).length;
      },
    },
    firestore: {
      listaNotas: db.collection('notas')
    }
}


</script>

<style scoped>
  .lista-notas{
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

  .estado{
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

  .cuerpo ol li{
    border: 1px solid black;
    margin: 1rem;
    display: flex;
    align-content: center;
    padding: 1rem;
    border-radius: 5%;
  }

  .cuerpo ol li img{
    border: 1px solid black;
    background: white;
    padding: 0.2rem;
    cursor: pointer;
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
