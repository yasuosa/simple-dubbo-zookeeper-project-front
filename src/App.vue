<template>
  <div id="app">
    <Bills msg="Welcome to Your Vue.js App"/>
  </div>
</template>

<script>
import Bills from './components/index.vue'

export default {
  name: 'App',
  components: {
    Bills
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
