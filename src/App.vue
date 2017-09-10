<template>
  <div id="app">
    <div id="stage">
      <svg width="800" height="300" v-on:click="clickStage" data-type="stage">
        <edge v-for="(edge, key) in edges"
              :key="key"
              :id="key"
              :x1="computers[edge.nodes[0]].x"
              :y1="computers[edge.nodes[0]].y"
              :x2="computers[edge.nodes[1]].x"
              :y2="computers[edge.nodes[1]].y"
              :classobject="edge.classObject"
              type="edge"/>

        <edge v-if="bindedNode"
              :x1="computers[bindedNode].x"
              :y1="computers[bindedNode].y"
              :x2="mouseX - 20"
              :y2="mouseY - 20"
              type="stage"/>

        <computer v-for="(computer, key) in computers"
                  :key="key"
                  :id="key"
                  :name="computer.name"
                  :ip="computer.ip"
                  :mac="computer.mac"
                  :class="{ selected: computer.selected }"
                  :classobject="computer.classObject"
                  :x="computer.x"
                  :y="computer.y"
                  :selectNode="selectNode"/>
      </svg>
    </div>

    <addPopup v-if="newNodePos" :id="nextComputerId" :pos="newNodePos" :addNode="addNode"/>

    <button v-on:click="setAction('ADD')" :disabled="selectedNode || binding || senderNode">Add</button>
    <button v-on:click="setAction('DELETE')" :disabled="selectedNode || binding || senderNode">Delete</button>
    <button v-on:click="setAction('BIND')" :disabled="selectedNode || senderNode">Bind</button>
    <button v-on:click="setAction('SEND')" :disabled="selectedNode || binding">Send ARP</button>
    {{ currentAction }}
    <computer-table :table="(computers[selectedNode]) ? computers[selectedNode].table : null"></computer-table>
  </div>
</template>

<script>
  import Computer from './components/Computer'
  import Edge from './components/Edge'
  import ComputerTable from './components/ComputerTable'
  import addPopup from './components/addPopup'

  export default {
    name: 'app',
    components: {
      Computer,
      Edge,
      ComputerTable,
      addPopup
    },
    data: () => {
      return {
        nextComputerId: 1,
        selectedNode: null,
        currentAction: null,
        bindedNode: null,
        senderNode: null,
        newNodePos: null,
        binding: false,
        mouseX: 0,
        mouseY: 0,
        offset: {
          left: 0,
          top: 0
        },
        edges: {},
        computers: {}
      }
    },
    methods: {
      runSearch(start, value){
        const that = this
        const current = parseInt(start)
        let path = [current]

        this.clear()

        if(!this.computers[current]) return

        this.computers[current].neighbours.forEach((neighbourId) => {
          const result = this.search(value, neighbourId, path, [current])

          if(result){
            result.unshift(current)
            const unique = result.filter((item, i, array) => {
              return array.indexOf(item) === i
            })

            this.$set(this.computers[current].routes, value, unique)
            this.$set(this.computers[current].table, unique[unique.length-1], { ip: value, mac: this.computers[unique[unique.length-1]].mac })

            unique.reverse().reduce((prev, next) => {
              setTimeout(()=>{
                that.computers[prev].classObject.red = false
                that.computers[prev].classObject.green = true
                that.greenLine(prev, next)
              }, 1000)
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
        this.computers[id].classObject.red = true
        this.redline(previous[0], id)

        if(this.computers[id].ip === value){
          return current
        }else{
          const sorted = this.computers[id]
                              .neighbours
                              .filter((item) => {
                                if(!visited.includes(item)) return true
                                return false
                              })

          for(let key in sorted){
            var next = this.search(value, sorted[key], current, visited)
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
      },
      selectNode(id){
        if(this.selectedNode) this.computers[this.selectedNode].selected = false
        if(this.selectedNode === id) return this.selectedNode = null
        this.computers[id].selected = true
        this.selectedNode = id
      },
      addNode(x, y, name, ip, mac){
        this.newNodePos = null
        this.$set(this.computers, this.nextComputerId, {
          name: name,
          ip: ip,
          mac: mac,
          neighbours: [],
          classObject: {
            red: false,
            green: false
          },
          x: x - 20,
          y: y - 20,
          table: {},
          routes: {},
          selected: false
        })
        this.nextComputerId ++
      },
      deleteNode(id){
        const that = this
        this.computers[id].neighbours.forEach((computer) => {
          const min = Math.min(id, computer)
          const max = Math.max(id, computer)
          this.$delete(this.edges, `${min}-${max}`)

          const filtered = this.computers[computer].neighbours.filter((neighbourId) => {
            return neighbourId != id
          })
          this.$set(this.computers[computer], 'neighbours', filtered)
        })
        this.$delete(this.computers, id)
      },
      addEdge(a, b){
        const min = Math.min(a, b)
        const max = Math.max(a, b)

        if(this.edges[`${min}-${max}`]) return false

        this.computers[min].neighbours.push(max)
        this.computers[max].neighbours.push(min)

        this.$set(this.edges, `${min}-${max}`, {
          nodes: [a, b],
          classObject: {
            red: false,
            green: false
          }
        })
      },
      deleteEdge(key){
        const min = key.split('-')[0]
        const max = key.split('-')[1]

        const minFiltered = this.computers[min].neighbours.filter((neighbourId) => {
          return neighbourId != max
        })

        const maxFiltered = this.computers[max].neighbours.filter((neighbourId) => {
          return neighbourId != min
        })

        this.$set(this.computers[min], 'neighbours', minFiltered)
        this.$set(this.computers[max], 'neighbours', maxFiltered)

        this.$delete(this.edges, key)
      },
      setAction(action){
        this.currentAction = (this.currentAction === action) ? null : action
        if(action === 'BIND'){
          this.bindedNode = null
          this.binding = !this.binding
        }
      },
      clickStage(event){
        const data = event.target.dataset
        switch(data.type){
          case 'computer':
            if(this.currentAction === 'DELETE'){
              this.deleteNode(data.id)
            }else if(this.currentAction === 'BIND'){
              if(this.bindedNode === data.id){
                this.computers[this.bindedNode].selected = false
                this.bindedNode = null
                window.removeEventListener('mousemove', this.moveBindLine)
              }else if(this.bindedNode){
                this.addEdge(this.bindedNode, data.id)
                this.computers[this.bindedNode].selected = false
                this.bindedNode = null
                window.removeEventListener('mousemove', this.moveBindLine)
              }else{
                this.bindedNode = data.id
                this.computers[this.bindedNode].selected = true
                this.mouseX = this.computers[this.bindedNode].x + 20
                this.mouseY = this.computers[this.bindedNode].y + 20
                window.addEventListener('mousemove', this.moveBindLine)
              }
            }else if(this.currentAction === 'SEND'){
              if(this.senderNode == data.id){
                this.computers[this.senderNode].selected = false
                this.senderNode = null
              }else if(this.senderNode){
                this.runSearch(this.senderNode, this.computers[data.id].ip)
                this.computers[this.senderNode].selected = false
                this.senderNode = null
              }else{
                this.senderNode = data.id
                this.computers[this.senderNode].selected = true
              }
            }else if(!this.currentAction){
              this.selectNode(data.id)
            }
          break
          case 'edge':
            if(this.currentAction === 'DELETE'){
              this.deleteEdge(data.id)
            }
          break
          case 'stage':
            if(this.currentAction === 'BIND' && this.bindedNode){
              this.computers[this.bindedNode].selected = false
              this.bindedNode = null
              window.removeEventListener('mousemove', this.moveBindLine)
            }else if(this.currentAction === 'ADD'){
              this.newNodePos = {
                x: event.clientX - this.offset.left,
                y: event.clientY - this.offset.top
              }
            }
          break
        }
      },
      moveBindLine(event){
        this.mouseX = event.clientX - this.offset.left
        this.mouseY = event.clientY - this.offset.top
      }
    },
    mounted(){
      this.offset.left = document.getElementById('stage').offsetLeft
      this.offset.top = document.getElementById('stage').offsetTop
    }
  }

</script>

<style scoped>
  #app {
    max-width: 800px;
    margin: 60px auto 0;
  }
</style>
