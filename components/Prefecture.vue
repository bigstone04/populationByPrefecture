<template>
  <div class="prefecture_margin">
    <h1>都道府県</h1>
    <div class="prefecture_area">
      <div v-for="prefecture in prefectures" :key="prefecture.id">
        <label :for="prefecture.id">
          <input
            :id="prefecture.id"
            type="checkbox"
            :checked="prefecture.isChecked"
            @click="switchAction(prefecture.id, prefecture.name, prefecture.isChecked)"
          />
          {{ prefecture.name }}
        </label>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
data() {
    return {
      prefectures: []
    }
  },
  mounted() {
    this.initPrefectures()
  },
  methods: {
    /* 県の情報を取得 */
    getData(path) {
      const response = axios.get(
        `https://opendata.resas-portal.go.jp/api/v1/${path}`,
        {
          headers: { "X-API-KEY": process.env.RESAS_API_KEY }
        }
      )
      return response
    },
    /* 県の表示の初期化 */
    async initPrefectures() {
      const path = "prefectures"
      try {
        const response = await this.getData(path)
        this.prefectures = response.data.result.map(val => {
          return {
            id: val.prefCode,
            name: val.prefName,
            isChecked: false
          }
        })
      } catch (error) {
        console.error(error.message)
      }
    },
    /* グラフを追加 */
    async addPopulationData(id, name) {
      const path = `population/composition/perYear?cityCode=-&prefCode=${id}`
      try {
        const response = await this.getData(path)
        const population = response.data.result.data[0].data.map(
          val => val.value
        )
        this.$emit("onAddPopulationData", id, name, population);
        this.prefectures[id - 1].isChecked = true;
      } catch (error) {
        console.error(error.message) 
      }
    },
    /* グラフを削除 */
    removePopulationData(id) {
      this.$emit("onRemovePopulationData", id);
      this.prefectures[id - 1].isChecked = false;
    },
    switchAction(id,name,isChecked){
      if(!isChecked){
        this.addPopulationData(id,name)
      } else {
        this.removePopulationData(id)
      }
    }
  }
}
</script>

<style scoped>
.prefecture_area{
  display: grid;  
  justify-items: start;
  align-items: center;
  gap: 10px;
  grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
  font-size: 18px;
}
.prefecture_margin{
  margin-right: 20px;
  margin-left: 20px;
}
label {
  cursor: pointer;
}
</style>
