<template>
  <div class="calculator-display">
      <div class="input">{{ input }}</div>
      <div class="result">{{ result }}</div>
  </div>
</template>

<script>
export default {
  name: 'DisplayCalculator',
  props: {
      input: String,
      result: String,
  }
};
</script>

<style scoped>
.calculator-display {
  text-align: right;
  padding: 20px 40px;
}

.input {
  font-family: 'Inconsolata', monospace;
  color: #7D7D7D;
  font: 20px;
}

.result {
  font-family: 'Inconsolata', monospace;
  color: white;
  font-size: 40px;
}
</style>