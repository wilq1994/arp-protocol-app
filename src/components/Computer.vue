<template>
  <g>
    <text :x="x + 20"
          :y="y - 10"
          class="text"
          data-type="stage">
          {{ name }}
    </text>

    <rect :x="x"
          :y="y"
          width="40"
          height="40"
          :data-id="id"
          data-type="computer"
          :class="classobject"/>

    <text :x="x + 20"
          :y="y + 60"
          class="text"
          data-type="stage">
          {{ ip }}
    </text>

    <text :x="x + 20"
          :y="y + 80"
          class="text"
          data-type="stage">
          {{ mac }}
    </text>
  </g>
</template>

<script>
  export default {
    name: 'computer',
    props: ['id', 'x', 'y', 'name', 'ip', 'mac', 'classobject', 'selectNode']
  }
</script>

<style scoped>
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
