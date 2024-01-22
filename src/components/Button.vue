<template>
  <button @click="handleClick">
    {{ label }}
  </button>
</template>
  
<script>
export default {
  name: 'ButtonCalculator',
  props: {
    label: String,
  },
  methods: {
    handleClick() {
      this.$emit('button-click', this.label);
    },
  },
};
</script>
  
<style lang="scss" scoped>
button {
  font-family: 'Inconsolata', monospace;
  background-color: transparent;
  border: none;
  color: white;
  font-size: 18px;

  &:active {
    background-color: rgb(129, 129, 129);
    color: white;
    border-radius: 10px;
  }
}
</style>