<template>
  <div id="app">
    <h2>IndexedDB Sample / Address Search</h2>
    <button v-if="!isInitDB" @click="clickInitBtn">IndexedDB SETUP</button>
    <div v-else>
      <search-form @searchFilter="searchFilter"/>
      <p>{{ message }}</p>
      <item-table :disp-data="dispData"/>
    </div>
  </div>
</template>

<script>
import jsonData from "./assets/address.json";
import itemTable from "./component/itemTable.vue";
import searchForm from "./component/searchForm.vue";
import Dexie from "dexie";

export default {
  name: "App",
  data() {
    return {
      dbName: "addressDB",
      db: "",
      isInitDB: false,
      dispData: [],
      json: jsonData,
      message: "検索データload中…"
    };
  },
  components: {
    itemTable,
    searchForm
  },
  mounted() {},
  methods: {
    clickInitBtn() {
      this.isInitDB = true;
      this.setInitDB();
    },

    async setInitDB() {
      try {
        let beginTime = new Date().getTime();
        let loadMessage = "setup完了(2回目以降アクセス)";

        // DB open
        this.db = new Dexie(this.dbName);
        this.db.version(1).stores({
          addresTable: "++id, zipcode, address"
        });
        await this.db.open();

        let tableItemCount = await this.db.addresTable.count();

        // first access data insert
        if (tableItemCount === 0) {
          loadMessage = "検索データload＆setup完了(初回アクセス)";
          await this.db.addresTable.bulkAdd(this.json);
          tableItemCount = await this.db.addresTable.count();
        }

        this.dispData = await this.db.addresTable.orderBy("id").toArray();

        const dispResultTime = (new Date().getTime() - beginTime) / 1000;

        this.message = `${loadMessage}: ${tableItemCount}件 / ${dispResultTime}s`;
      } catch (e) {
        this.message += `db open error:${e}<br />`;
      }
    },

    async searchFilter(filterInfo) {
      let beginTime = new Date().getTime();
      const filterResult = await this.db.addresTable.filter(v => {
        return v[filterInfo.target].includes(filterInfo.word);
      });

      const resultCount = await filterResult.count();
      this.dispData = await filterResult.toArray();

      const dispResultTime = (new Date().getTime() - beginTime) / 1000;
      this.message = `検索結果: ${resultCount}件 / ${dispResultTime}s`;
    }
  }
};
</script>

<style>
* {
  background: #72bfea;
  -moz-appearance: none;
  -webkit-appearance: none;
  appearance: none;
  border-radius: 0;
  font-family: "Avenir", "Helvetica Neue", "Helvetica", "Arial", "Hiragino Sans",
    "ヒラギノ角ゴシック", YuGothic, "Yu Gothic", "メイリオ", Meiryo,
    "ＭＳ Ｐゴシック", "MS PGothic";
  font-weight: bold;
}

select,
input,
button {
  background: #fff;
  color: #3f3f3f;
  cursor: pointer;
  margin: 3px;
  font-size: 14px;
  border: none;
  line-height: 1.8em;
  padding: 0 6px;
}

#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #fff;
  margin-top: 60px;
}
</style>
