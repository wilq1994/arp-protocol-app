<template lang="html">
  <div class="popup">
    <div class="popup-bg"></div>
    <div class="popup-content">
      <div class="popup-form">
        <label>Name:</label>
        <input type="text" v-model="name">
        <label>IP:</label>
        <input type="text" v-model="ip">
        <label class="disabled">MAC:</label>
        <input type="text" :value="MACAddress" class="disabled" disabled>
        <button v-on:click="add" ref="btnAdd" class="btn-add">Add new node</button>
        <button v-on:click="cancel" class="btn-cancel">Cancel</button>
      </div>
    </div>
  </div>
</template>

<script>
  export default {
    name: 'add-popup',
    props: ['id', 'pos', 'addNode', 'closePopup'],
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
      },
      cancel(){
        this.closePopup()
      }
    },
    mounted: function(){
        this.$refs.btnAdd.focus()
    }
  }
</script>

<style lang="css">
  .popup {
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
  }

  .popup-bg {
    background: rgba(0,0,0,0.5);
    position: absolute;
    top: 0;
    left: 0;
    right: 0;
    bottom: 0;
  }

  .popup-content {
    position: absolute;
    top: 0;
    left: 0;
    right: 300px;
    bottom: 0;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  .popup-form {
    background: #2f2f2f;
    padding: 1rem;
    width: 200px;
    border: 1px solid #4a4a4a;
  }

  .popup-form label {
    display: block;
    margin-bottom: 0.5rem;
  }

  .popup-form input[type="text"] {
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

  .popup-form input[type="text"]:focus {
    border-bottom: 1px solid #fff;
  }

  .disabled {
    opacity: 0.4;
  }

  .btn-add {
    background: #44f76f;
    display: block;
    width: 100%;
    padding: 0.5rem;
    margin-bottom: 0.5rem;
    border: 0;
    cursor: pointer;
  }
    .btn-add:hover {
      background: #44f76f;
    }

  .btn-cancel {
    background: #f74545;
    color: #fff;
    display: block;
    width: 100%;
    padding: 0.5rem;
    border: 0;
    cursor: pointer;
  }
    .btn-cancel:hover {
      background: #f74545;
    }
</style>
