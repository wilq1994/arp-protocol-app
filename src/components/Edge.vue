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
    <image v-if="animating"
          xlink:href="static/envelope.png"
          :x="envelope.x + 20"
          :y="envelope.y + 30"
          :width="62"
          :height="38"
          fill="#000"/>
  </g>
</template>

<script>
  export default {
    name: 'edge',
    props: ['id', 'x1', 'y1', 'x2', 'y2', 'classobject', 'animation', 'type'],
    data() {
      return {
        animating: false,
        currentTime: 0,
        envelope: {
          x: this.x1,
          y: this.y1
        }
      }
    },
    watch: {
      animation(){
        this.startAnimation()
      }
    },
    methods: {
      startAnimation(){
        const that = this
        this.currentTime = 0
        this.animating = true

        function loop(){
          that.envelope.x = that.linearTween(that.currentTime, that.animation.start.x, that.animation.end.x, 50)
          that.envelope.y = that.linearTween(that.currentTime, that.animation.start.y, that.animation.end.y, 50)
          if(that.currentTime<50){
            requestAnimationFrame(loop)
            that.currentTime ++
          }else{
            that.animating = false
          }
        }
        requestAnimationFrame(loop)
      },
      linearTween(t, b, c, d) {
      	return t/d*(c-b) + b;
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
