<template>
  <g v-on:click="clickEvent">
    <text :x="x + 20" :y="y - 10" class="text">PC{{ id }}</text>
    <rect :x="x" :y="y" width="40" height="40" :data-id="id" :class="classobject"/>
    <text :x="x + 20" :y="y + 60" class="text">192.168.0.1</text>
    <text :x="x + 20" :y="y + 80" class="text">E4:CD:8A:83:1D:0B</text>
  </g>
</template>

<script>
  export default {
    name: 'computer',
    props: ['id', 'x', 'y', 'value', 'classobject', 'selectNode'],
    data () {
      return {
      }
    },
    methods: {
      clickEvent(){
        this.selectNode(parseInt(this.id))
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

  .text {
    font-family: 'Arial';
    font-size: 12px;
    text-anchor: middle;
  }

  .selected {
    opacity: .3;
  }
</style>
