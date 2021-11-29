<template>
  <div class="prefecture_Area">
    <div v-for="prefecture in prefectures" :key="prefecture.id">
      <label :for="prefecture.id">
        <input
          :id="prefecture.id"
          type="checkbox"
          :checked="prefecture.isChecked"
        />
        {{ prefecture.name }}
      </label>
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
    }
  }
}
</script>

<style scoped>
.prefecture_Area{
  display: grid;  
  margin-right: 20px;
  margin-left: 20px;
  justify-items: start;
  align-items: center;
  gap: 10px;
  grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
  font-size: 18px;
}
label {
  cursor: pointer;
}
</style>
