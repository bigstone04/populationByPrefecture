<template>
  <div>
    <h1 class="header">都道府県</h1>
    <Prefecture  @onAddPopulationData="addPopulationData"/>
    <highchart :options="options" />
  </div>
</template>

<script>
export default {
  data() {
    return {
      /* nuxt-highchartsのプロパティ */
      options: {
        series: [],
        title: {
          style: {
            display: "none"
          }
        },
        legend: {
          align: "right",
          verticalAlign: "top",
          layout: "vertical"
        },
        xAxis: {
          title: {
            text: "年度"
          },
          categories: [/* 年度はしてしないとグラフの値がずれる */
            1960,
            1965,
            1970,
            1975,
            1980,
            1985,
            1990,
            1995,
            2000,
            2005,
            2010,
            2015,
            2020,
            2025,
            2030,
            2035,
            2040,
            2045
          ]
        },
        yAxis: {
          title: {
            text: "人口数"
          }
        }
      }
    }
  },
  methods: {
    /* 人口データをグラフに追加する */
    addPopulationData(id, name, population) {
      this.options.series.push({id,name,data: population})
    },
    /* 特定の人口データをグラフから取り除く */
    removePopulationData(id) {
      this.options.series = this.options.series.filter(val => val.id !== id);
    }
  }
}
</script>

<style scoped>
 .header {
    text-align: center;
    background-color: darkgray;
    margin: 0%;
    padding: 0%;
    height: auto;
    width: vw;
 }
</style>
