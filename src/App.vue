<template>
  <div>
    <!-- 九宫格 -->
    <Lucky />
  </div>
</template>

<script>
import Lucky from './components/Lucky.vue'
export default {
  name: 'App',
  components: {
    Lucky
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
}
div{
  padding: 0;
  margin: 0;
}
</style>
