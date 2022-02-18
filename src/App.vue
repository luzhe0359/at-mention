<template>
  <div id="app">
    <index />
  </div>
</template>

<script>
import index from './view/index'

export default {
  name: 'App',
  components: {
    index
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  height: 100vh;
  width: 100%;
}
</style>
