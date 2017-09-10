<template lang="html">
  <div class="popup">
    <label>Name:</label><br>
    <input type="text" v-model="name">
    <br><br>
    <label>IP:</label><br>
    <input type="text" v-model="ip">
    {{ ip }}
    <br><br>
    <label>MAC:</label><br>
    <input type="text" :value="MACAddress" disabled>
    <br><br>
    <button v-on:click="add">Add new node</button>
  </div>
</template>

<script>
  export default {
    name: 'addPopup',
    props: ['id', 'pos', 'addNode'],
    data: function(){
      return {
        name: 'PC' + this.id,
        ip: '192.168.0.' + this.id
      }
    },
    computed: {
      MACAddress(){
        var hexDigits = "0123456789ABCDEF";
        var macAddress = "";
        for (var i = 0; i < 6; i++) {
            macAddress+=hexDigits.charAt(Math.round(Math.random() * 15));
            macAddress+=hexDigits.charAt(Math.round(Math.random() * 15));
            if (i != 5) macAddress += ":";
        }
        return macAddress;
      }
    },
    methods: {
      add(){
        this.addNode(this.pos.x, this.pos.y, this.name, this.ip, this.MACAddress)
      }
    }
  }
</script>

<style lang="css">
</style>
