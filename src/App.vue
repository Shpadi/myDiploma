<template>
  <div id="app">
    <Graph/>
  </div>
</template>

<script>
import Vue from 'vue'
import Graph from './components/Craph.vue'


Vue.component('Graph',Graph);
export default {
  name: 'app',

}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
