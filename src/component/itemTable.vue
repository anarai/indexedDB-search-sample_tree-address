<template>
  <table id="itemTable">
    <tr>
      <th>郵便番号</th>
      <th>住所</th>
    </tr>
    <tr v-for="item in dispData" :key="item.id">
      <td>{{item.zipcode}}</td>
      <td>{{item.address}}</td>
    </tr>
  </table>
</template>

<script>
export default {
  props: {
    dispData: {
      type: Array,
      required: false,
      default: () => []
    }
  }
};
</script>

<style>
#itemTable {
  border-collapse: collapse;
  border-spacing: 0;
  width: 100%;
  text-align: left;
}
#itemTable tr {
  border-bottom: solid 1px #fff;
}
#itemTable th,
#itemTable td {
  padding: 15px 5px;
}
</style>
