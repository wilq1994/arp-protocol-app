<template>
  <div class="computer">
    <div class="computer__status"></div>
    <div>Computer {{ id }}</div>
    <div>Value {{ value }}</div>
  </div>
</template>

<script>
  export default {
    name: 'computer',
    props: ['id', 'value'],
    data () {
      return {
      }
    }
  }
</script>

<style scoped>
  .computer__status {
    background: #000;
    width: 20px;
    height: 20px;
    border-radius: 20px;
  }
</style>
