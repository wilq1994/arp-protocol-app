<template lang="html">
  <div id="editForm">
    <h2>Edit</h2>
    <label>Name:</label>
    <input type="text" v-model="newName">
    <label>IP:</label>
    <input type="text" v-model="newIp">
    <label class="disabled">MAC:</label>
    <input class="disabled" type="text" :value="mac" disabled>
    <button class="btn-update" v-on:click="update">Update</button>
  </div>
</template>

<script>
  export default {
    name: 'edit-form',
    props: ['name', 'ip', 'mac', 'updateNode'],
    data: function(){
      return {
        newName: this.name,
        newIp: this.ip
      }
    },
    watch: {
      name(val){
        this.newName = val
      },
      ip(val){
        this.newIp = val
      }
    },
    methods: {
      update(){
        this.updateNode(this.newName, this.newIp)
      }
    }
  }
</script>

<style>
  #editForm {
    padding: 1rem 1rem 2rem;
  }

  h2 {
    font-size: 26px;
    font-weight: 400;
    margin: 0 0 1rem;
  }

  label {
    display: block;
    margin-bottom: 0.5rem;
  }

  input[type="text"] {
    background: rgba(255, 255, 255, 0.1);
    color: #fff;
    display: block;
    padding: 0.5rem;
    border: 0;
    outline: 0;
    width: 100%;
    box-sizing: border-box;
    margin-bottom: 1rem;
    border-bottom: 1px solid transparent;
  }

  input[type="text"]:focus {
    border-bottom: 1px solid #fff;
  }

  .disabled {
    opacity: 0.4;
  }

  .btn-update {
    background: #44f76f;
    display: block;
    width: 100%;
    padding: 0.5rem;
    margin-bottom: 0.5rem;
    border: 0;
    cursor: pointer;
  }
    .btn-update:hover {
      background: #44f76f;
    }
</style>
