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
            @click="getPopulationData(prefecture.id, prefecture.name)"
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
    getPrefectures() {
      const response = axios.get(
        `https://opendata.resas-portal.go.jp/api/v1/prefectures`,
        {
          headers: { "X-API-KEY": process.env.RESAS_API_KEY }
        }
      )
      return response
    },
    /* 県の表示の初期化 */
    async initPrefectures() {
      try {
        const response = await this.getPrefectures()
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
    async getPopulationData(id, name) {
      try {
        const response = await axios.get(
          `https://opendata.resas-portal.go.jp/api/v1/population/composition/perYear?cityCode=-&prefCode=${id}`,
          {
            headers: { "X-API-KEY": process.env.RESAS_API_KEY }
          }
        )
        const population = response.data.result.data[0].data.map(
          val => val.value
        )
        this.$emit("onAddPopulationData", id, name, population);
        this.prefectures[id - 1].isChecked = true;
      } catch (error) {
        console.error(error.message) 
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
