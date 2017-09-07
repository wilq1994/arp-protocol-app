<template>
  <table>
    <thead>
      <tr>
        <th>Adres IP</th>
        <th>Adres MAC</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="(item, key) in table">
        <td>{{ item.ip }}</td>
        <td>{{ item.mac }}</td>
      </tr>
    </tbody>
  </table>
</template>

<script>
export default {
  name: 'computer-table',
  props: ['table']
}
</script>

<style lang="css">
</style>
