<template>
  <g>
    <line class="line"
          :x1="x1 + 20"
          :y1="y1 + 20"
          :x2="x2 + 20"
          :y2="y2 + 20"
          :class="classobject"
          :data-id="id"
          :data-type="type"/>
  </g>
</template>

<script>
  export default {
    name: 'edge',
    props: ['id', 'x1', 'y1', 'x2', 'y2', 'classobject', 'type'],
    data () {
      return {
      }
    }
  }
</script>

<style scoped>
  .line {
    stroke: #000;
    stroke-width: 3;
  }

  .red {
    stroke: #ff0000;
  }

  .green {
    stroke: #00ff00;
  }
</style>
