<template>
  <div id="app">
    <Cabecera/>
    <listaNotas/>
    <Pie/>
  </div>
</template>

<script>

import Cabecera from './components/cabecera.vue'
import listaNotas from './components/listaNotas.vue'
import Pie from './components/pie.vue'

export default {
  name: 'App',
  components: {
    Cabecera,
    listaNotas,
    Pie
  }
}
</script>

<style>
  body{
    margin: 0;
    padding: 0;
    background: cyan;
    font-family: sans-serif;
    font-size: large;
  }
</style>
