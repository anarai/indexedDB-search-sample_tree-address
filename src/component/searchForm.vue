<template>
  <div id="searchForm">
    <div class="select-box">
      <select v-model="filterInfo.target">
        <option value="address">住所で検索</option>
        <option value="zipcode">郵便番号で検索</option>
      </select>
    </div>
    <input type="text" placeholder="例：六本木" v-model="filterInfo.word">
    <button @click="setFilter">検索</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      filterInfo: {
        target: "address",
        word: ""
      }
    };
  },
  methods: {
    setFilter() {
      this.$emit("searchFilter", this.filterInfo);
    }
  }
};
</script>

<style>
.select-box {
  position: relative;
  display: inline-block;
}
.select-box::after {
  content: "";
  width: 5px;
  height: 5px;
  border: 0px;
  border-bottom: solid 2px #b4b3b3;
  border-right: solid 2px #b4b3b3;
  transform: rotate(45deg);
  position: absolute;
  top: 50%;
  right: 13px;
  margin-top: -4px;
}

select {
  width: 140px;
}
</style>
