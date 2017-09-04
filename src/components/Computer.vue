<template>
  <div class="computer0" :class="classobject">
    <div class="computer__status"></div>
    <div>Computer {{ id }}</div>
    <div>Value {{ value }}</div>
  </div>
</template>

<script>
  export default {
    name: 'computer',
    props: ['id', 'value', 'classobject'],
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

  .red .computer__status {
    background: #ff0000;
  }

  .green .computer__status {
    background: #00ff00;
  }
</style>
