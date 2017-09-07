<template>
  <rect :x="x" :y="y" width="40" height="40" :class="classobject"/>
</template>

<script>
  export default {
    name: 'computer',
    props: ['id', 'x', 'y', 'value', 'classobject'],
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

  .red {
    fill: #ff0000;
  }

  .green {
    fill: #00ff00;
  }
</style>
