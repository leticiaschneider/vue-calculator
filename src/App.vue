<template>
  <Calculator />
</template>

<script>
import Calculator from './components/Calculator.vue'

export default {
  name: 'App',
  components: {
    Calculator
  }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Inconsolata&display=swap');
body {
  margin: 0;
  padding: 0;
}
#app {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background-image: linear-gradient(to right top, #e0e0e0, #d0d0d0, #c0c0c0, #b0b0b0, #a1a1a1);}
</style>