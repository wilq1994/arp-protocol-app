<template>
  <g>
    <line class="line"
          :x1="x1 + 50"
          :y1="y1 + 50"
          :x2="x2 + 50"
          :y2="y2 + 50"
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
    stroke: #606060;
    stroke-width: 3;
    transition: stroke 0.3s ease;
  }

  .red {
    stroke: #ff0000;
  }

  .green {
    stroke: #00ff00;
  }
</style>
