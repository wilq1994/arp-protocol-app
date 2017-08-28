<template>
  <div id="app">
    <div class="list-computers">
      <computer v-for="(computer, key) in computers" :key="key" :id="key" :value="computer.value"></computer>
    </div>
    <button v-on:click="runSearch(1, 'b')">Start</button>
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
            neighbours: [2]
          },
          2: {
            value: 'b',
            neighbours: [1, 3]
          },
          3: {
            value: 'c',
            neighbours: [2,4]
          },
          4: {
            value: 'd',
            neighbours: [3]
          }
        }
      }
    },
    methods: {
      runSearch(target, value){
        let _p = [1]
        console.log(this.search('d', 2, _p, [1]))
      },
      search(value, id, path, visited = []){
        if(visited.includes(id)) return false

        const that = this;
        const _path = [id];
        visited.push(id)

        if(this.computers[id].value === value){
          return true
        }else{
          const sorted = this.computers[id]
                              .neighbours
                              .filter((item) => {
                                if(!visited.includes(item)) return true
                                return false
                              })

          for(let key in sorted){
            if(this.search(value, sorted[key], _path, visited)){
              _path.push(sorted[key])
              console.log(path.concat(_path));
              return true
            }
          }
        }

        return false
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
