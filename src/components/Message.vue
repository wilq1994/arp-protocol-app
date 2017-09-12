<template lang="html">
  <div id="message">
    <h2>ARP message</h2>
    <table v-if="message.sender">
      <thead>
        <tr>
          <th>Sender IP</th>
          <th>Sender MAC</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>{{ message.sender.ip }}</td>
          <td>{{ message.sender.mac }}</td>
        </tr>
      </tbody>
    </table>
    <table v-if="message.receiver">
      <thead>
        <tr>
          <th>Receiver IP</th>
          <th>Receiver MAC</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>{{ message.receiver.ip }}</td>
          <td>{{ message.receiver.mac }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
  export default {
    name: 'message',
    props: ['message']
  }
</script>

<style scoped>
  #message {
    padding: 1rem 1rem 2rem;
  }

  h2 {
    font-size: 26px;
    font-weight: 400;
    margin: 0 0 1rem;
  }

  table {
    border-collapse: collapse;
    border-spacing: 0;
    width: 100%;
  }

  th {
    background: #cec067;
    color: #000;
    width: 50%;
    padding: 0.5rem;
    text-transform: uppercase;
  }

  tbody tr:nth-child(2n) td {
    background: #353535;
  }

  td {
    padding: 0.75rem 0.5rem;
    text-align: center;
  }
</style>
