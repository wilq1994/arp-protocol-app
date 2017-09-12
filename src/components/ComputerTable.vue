<template>
  <div id="table">
    <h2>ARP Table</h2>
    <table>
      <thead>
        <tr>
          <th>IP</th>
          <th>MAC</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, key) in table">
          <td>{{ item.ip }}</td>
          <td>{{ item.mac }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  name: 'computer-table',
  props: ['table']
}
</script>

<style scoped>
  #table {
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
