<template>
  <g>
    <text :x="x + 50"
          :y="y - 10"
          class="text"
          data-type="stage">
          {{ name }}
    </text>

    <image :xlink:href="src"
          :x="x"
          :y="y"
          :data-id="id"
          :class="classobject"
          width="103"
          height="105"
          data-type="computer"
          v-on:mousedown="drag"/>

    <text :x="x + 50"
          :y="y + 120"
          class="text"
          data-type="stage">
          {{ ip }}
    </text>

    <text :x="x + 50"
          :y="y + 140"
          class="text"
          data-type="stage">
          {{ mac }}
    </text>
  </g>
</template>

<script>
  export default {
    name: 'computer',
    props: ['id', 'x', 'y', 'name', 'ip', 'mac', 'classobject', 'selectNode', 'dragComputer'],
    computed: {
      src(){
        let s = ''
        if(this.classobject.red) s = '-red'
        if(this.classobject.green) s = '-green'

        return `static/computer${s}.png`
      }
    },
    methods: {
      drag(){
        this.dragComputer()
      }
    }
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
    fill: #fff;
    font-family: 'Arial';
    font-size: 12px;
    text-anchor: middle;
  }

  .selected {
    opacity: .3;
  }
</style>
