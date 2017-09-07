<template>
  <div id="app">
    <svg width="800" height="300">
      <edge v-for="(edge, key) in edges" :x1="computers[edge.nodes[0]].x" :y1="computers[edge.nodes[0]].y" :x2="computers[edge.nodes[1]].x" :y2="computers[edge.nodes[1]].y" :classobject="edge.classObject"></edge>
      <computer v-for="(computer, key) in computers" :key="key" :id="key" :value="computer.value" :classobject="computer.classObject" :x="computer.x" :y="computer.y"></computer>
    </svg>
    <button v-on:click="runSearch(2, 'f')">Start</button>
  </div>
</template>

<script>
  import Computer from './components/Computer'
  import Edge from './components/Edge'

  export default {
    name: 'app',
    components: {
      Computer,
      Edge
    },
    data: () => {
      return {
        edges: {
          '1-2': {
            nodes: [1,2],
            classObject: {
              red: false,
              green: false
            }
          },
          '2-3': {
            nodes: [2,3],
            classObject: {
              red: false,
              green: false
            }
          },
          '3-4': {
            nodes: [3,4],
            classObject: {
              red: false,
              green: false
            }
          },
          '4-5': {
            nodes: [4,5],
            classObject: {
              red: false,
              green: false
            }
          },
          '3-6': {
            nodes: [3,6],
            classObject: {
              red: false,
              green: false
            }
          }
        },
        computers: {
          1: {
            value: 'a',
            neighbours: [2],
            classObject: {
              red: false,
              green: false
            },
            x: 0,
            y: 0
          },
          2: {
            value: 'b',
            neighbours: [1, 3],
            classObject: {
              red: false,
              green: false
            },
            x: 100,
            y: 0
          },
          3: {
            value: 'c',
            neighbours: [2,4,6],
            classObject: {
              red: false,
              green: false
            },
            x: 200,
            y: 0
          },
          4: {
            value: 'd',
            neighbours: [3,5],
            classObject: {
              red: false,
              green: false
            },
            x: 300,
            y: 0
          },
          5: {
            value: 'e',
            neighbours: [4],
            classObject: {
              red: false,
              green: false
            },
            x: 400,
            y: 0
          },
          6: {
            value: 'f',
            neighbours: [3],
            classObject: {
              red: false,
              green: false
            },
            x: 200,
            y: 100
          }
        }
      }
    },
    methods: {
      runSearch(start, value){
        const that = this
        const current = start
        let path = [current]

        this.clear()

        this.computers[current].neighbours.forEach((neighbourId) => {
          const result = this.search(value, neighbourId, path, [current])

          if(result){
            result.unshift(current)
            const reversed = result.filter((item, i, array) => {
                              return array.indexOf(item) === i
                            })
                            .reverse()

            reversed.reduce((prev, next) => {
              setTimeout(()=>{
                that.computers[prev].classObject.red = false;
                that.computers[prev].classObject.green = true;
              }, 1000)
              that.greenLine(prev, next)
              return next
            })
          }

        })
      },
      search(value, id, previous, visited = []){
        if(visited.includes(id)) return false

        const that = this
        const current = [id]
        let result = false
        visited.push(id)
        this.computers[id].classObject.red = true;
        this.redline(previous[0], id)

        if(this.computers[id].value === value){
          return current
        }else{
          const sorted = this.computers[id]
                              .neighbours
                              .filter((item) => {
                                if(!visited.includes(item)) return true
                                return false
                              })

          for(let key in sorted){
            var next = this.search(value, sorted[key], current, visited);
            if(next){
              result = previous.concat(current.concat(next))
            }
          }
        }

        return result
      },
      redline(a, b) {
        const min = Math.min(a, b)
        const max = Math.max(a, b)
        this.edges[min+'-'+max].classObject.red = true
        console.log(`red ${a} ${b}`)
      },
      greenLine(a, b) {
        const min = Math.min(a, b)
        const max = Math.max(a, b)
        this.edges[min+'-'+max].classObject.red = false
        this.edges[min+'-'+max].classObject.green = true
        console.log(`green ${a} ${b}`)
      },
      clear(){
        for(let key in this.edges){
          this.edges[key].classObject.red = false
          this.edges[key].classObject.green = false
        }

        for(let key in this.computers){
          this.computers[key].classObject.red = false
          this.computers[key].classObject.green = false
        }
      }
    }
  }

</script>

<style>
  #app {
    max-width: 800px;
    margin: 60px auto 0;
  }

  .list-computers {
    display: flex;
    justify-content: space-between;
  }
</style>
