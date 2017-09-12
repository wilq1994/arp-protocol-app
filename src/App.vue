<template>
  <div id="app">
    <div id="stage">
      <svg width="100%" height="100%" v-on:click="clickStage" data-type="stage" xmlns="http://www.w3.org/2000/svg" xmlns:xlink= "http://www.w3.org/1999/xlink">
        <edge v-for="(edge, key) in edges"
              :key="key"
              :id="key"
              :x1="computers[edge.nodes[0]].x"
              :y1="computers[edge.nodes[0]].y"
              :x2="computers[edge.nodes[1]].x"
              :y2="computers[edge.nodes[1]].y"
              :classobject="edge.classObject"
              :animation="edge.animation"
              type="edge"/>

        <edge v-if="bindedNode"
              :x1="computers[bindedNode].x"
              :y1="computers[bindedNode].y"
              :x2="mouseX - 50"
              :y2="mouseY - 50"
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
                  :selectNode="selectNode"
                  :dragComputer="dragComputer"/>
      </svg>
    </div>

    <div id="sidebar">
      <div class="nav">
        <button v-on:click="setAction('ADD')" class="add" :class="{ active: currentAction==='ADD' }" :disabled="selectedNode || binding || senderNode">Add</button>
        <button v-on:click="setAction('DELETE')" class="delete" :class="{ active: currentAction==='DELETE' }" :disabled="selectedNode || binding || senderNode">Delete</button>
        <button v-on:click="setAction('BIND')" class="bind" :class="{ active: currentAction==='BIND' }" :disabled="selectedNode || senderNode">Bind</button>
        <button v-on:click="setAction('SEND')" class="send" :class="{ active: currentAction==='SEND' }" :disabled="selectedNode || binding">Send ARP</button>
      </div>

      <edit-form v-if="selectedNode"
                  :name="computers[selectedNode].name"
                  :ip="computers[selectedNode].ip"
                  :mac="computers[selectedNode].mac"
                  :updateNode="updateNode"/>

      <computer-table v-if="selectedNode" :table="(computers[selectedNode]) ? computers[selectedNode].table : null"></computer-table>
    </div>

    <add-popup v-if="newNodePos" :id="nextComputerId" :pos="newNodePos" :addNode="addNode" :closePopup="closeAddPopup"/>
  </div>
</template>

<script>
  import Computer from './components/Computer'
  import Edge from './components/Edge'
  import ComputerTable from './components/ComputerTable'
  import AddPopup from './components/AddPopup'
  import EditForm from './components/EditForm'

  export default {
    name: 'app',
    components: {
      Computer,
      Edge,
      ComputerTable,
      AddPopup,
      EditForm
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
        finalDelay: 0,
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
          const result = this.search(value, neighbourId, path, [current], 1)

          if(result){
            result.unshift(current)
            const unique = result.filter((item, i, array) => {
              return array.indexOf(item) === i
            })

            this.$set(this.computers[current].routes, value, unique)
            this.$set(this.computers[current].table, unique[unique.length-1], { ip: value, mac: this.computers[unique[unique.length-1]].mac })

            unique.reverse().reduce((prev, next, i) => {
              setTimeout(()=>{
                that.computers[prev].classObject.red = false
                that.computers[prev].classObject.green = true
                that.greenLine(prev, next)
              }, this.finalDelay * 1000 + i * 1000)
              return next
            })
          }

        })

      },
      search(value, id, previous, visited = [], delay = 1){
        if(visited.includes(id)) return false

        const that = this
        const current = [id]
        let result = false
        visited.push(id)
        setTimeout(() => {
          that.computers[id].classObject.red = true
          that.redline(previous[0], id)
        }, delay * 1000)

        if(this.computers[id].ip === value){
          this.finalDelay = delay
          return current
        }else{
          const sorted = this.computers[id]
                              .neighbours
                              .filter((item) => {
                                if(!visited.includes(item)) return true
                                return false
                              })

          for(let key in sorted){
            var next = this.search(value, sorted[key], current, visited, delay + 1)
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
        this.edges[min+'-'+max].animation = {
          start: {
            x: this.computers[a].x,
            y: this.computers[a].y
          },
          end: {
            x: this.computers[b].x,
            y: this.computers[b].y
          }
        }
      },
      greenLine(a, b) {
        const min = Math.min(a, b)
        const max = Math.max(a, b)
        this.edges[min+'-'+max].classObject.red = false
        this.edges[min+'-'+max].classObject.green = true
        this.edges[min+'-'+max].animation = {
          start: {
            x: this.computers[a].x,
            y: this.computers[a].y
          },
          end: {
            x: this.computers[b].x,
            y: this.computers[b].y
          }
        }
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
          x: x - 50,
          y: y - 50,
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
      updateNode(newName, newIp){
        this.$set(this.computers[this.selectedNode], 'name', newName)
        this.$set(this.computers[this.selectedNode], 'ip', newIp)
        this.$set(this.computers[this.selectedNode], 'selected', false)
        this.selectedNode = null
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
          },
          animation: null
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
      },
      dragComputer(){
        if(this.selectedNode){
          document.querySelector('body').addEventListener('mousemove', this.moveComputer)
          document.querySelector('body').addEventListener('mouseup', this.dropComputer)
        }
      },
      moveComputer(event){
        this.computers[this.selectedNode].x = event.clientX - this.offset.left - 50
        this.computers[this.selectedNode].y = event.clientY - this.offset.top - 50
      },
      dropComputer(event){
        document.querySelector('body').removeEventListener('mousemove', this.moveComputer)
        document.querySelector('body').removeEventListener('mouseup', this.dropComputer)
      },
      closeAddPopup(){
        this.newNodePos = null
      }
    },
    mounted(){
      this.offset.left = document.getElementById('stage').offsetLeft
      this.offset.top = document.getElementById('stage').offsetTop
    }
  }

</script>

<style>
  html {
    height: 100%;
  }

  body {
    background: url('../static/bg.png') repeat;
    height: 100%;
    margin: 0;
    user-select: none;
    font-family: 'Arial';
    font-size: 12px;
    color: #fff;
  }

  #app {
    height: 100%;
    margin: 0;
    display: flex;
  }

  #stage {
    flex: 1 1 auto;
    display: flex;
    flex-direction: column;
  }

  #sidebar {
    background: #2f2f2f;
    width: 350px;
    flex: 0 0 350px;
    border-left: 1px solid #222222;
    padding: 0.5rem;
    display: flex;
    flex-direction: column;
  }

  .nav {
    display: flex;
    flex: 0 0 auto;
    margin-bottom: 1rem;
  }

  .nav button {
    color: #fff;
    background: transparent;
    font-family: 'Arial';
    font-size: 12px;
    outline: 0;
    border: 0;
    border-bottom: 1px solid #606060;
    flex: 0 0 25%;
    padding: 0.5rem;
    cursor: pointer;
  }
    .nav button.active {
      border-bottom: 1px solid #fff;
    }

    .nav button:hover:before, .nav button.active:before {
      background-position-y: 0px;
      opacity: 1;
    }

    .nav button[disabled="disabled"] {
      opacity: 0.2;
    }

    .nav button:last-child {
      border-right: 0;
    }

    .nav button:before {
      content: '';
      display: block;
      background: url('../static/nav-small.png') no-repeat;
      background-position-y: -50px;
      width: 50px;
      height: 50px;
      margin: 0 auto 5px;
      opacity: 0.5;
    }

    .nav .add:before { background-position-x: 0; }
    .nav .delete:before { background-position-x: -50px; }
    .nav .bind:before { background-position-x: -100px; }
    .nav .send:before { background-position-x: -150px; }

  svg {
    flex: 1 1 auto;
  }
</style>
