<template>
  <div id="app">
    <div class="list-computers">
      <computer v-for="(computer, key) in computers" :key="key" :id="key" :value="computer.value" :classobject="computer.classObject"></computer>
    </div>
    <button v-on:click="runSearch(2, 'e')">Start</button>
  </div>
</template>

<script>
  import Computer from './components/Computer'

  export default {
    name: 'app',
    components: {
      Computer
    },
    data: () => {
      return {
        computers: {
          1: {
            value: 'a',
            neighbours: [2],
            classObject: {
              red: false,
              green: false
            }
          },
          2: {
            value: 'b',
            neighbours: [1, 3],
            classObject: {
              red: false,
              green: false
            }
          },
          3: {
            value: 'c',
            neighbours: [2,4,6],
            classObject: {
              red: false,
              green: false
            }
          },
          4: {
            value: 'd',
            neighbours: [3,5],
            classObject: {
              red: false,
              green: false
            }
          },
          5: {
            value: 'e',
            neighbours: [4],
            classObject: {
              red: false,
              green: false
            }
          },
          6: {
            value: 'f',
            neighbours: [3],
            classObject: {
              red: false,
              green: false
            }
          }
        }
      }
    },
    methods: {
      runSearch(start, value){
        const that = this
        const current = start
        let path = [current]


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
        console.log(`red ${a} ${b}`)
      },
      greenLine(a, b) {
        console.log(`green ${a} ${b}`)
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
