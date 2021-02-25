<template>
  <div id="app" class="container">
    <div class="m20" v-for="(data, index) in appData" :key="index">
      <chart
        :width="200"
        :height="200"
        :margin="20"
        :chartId="`chart-${index}`"
        :chartTitle="data.title"
        :chartData="data"
      />
    </div>
  </div>
</template>

<script>
import Chart from "./components/chart/Chart.vue";
import appData from "./data/data.json";

export default {
  name: "App",
  components: {
    Chart,
  },
  data() {
    return {
      appData: appData,
    };
  },
};
</script>

<style>
#app {
  font-family: "Open Sans Condensed", sans-serif;
}

.container {
  display: flex;
}

.row {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  width: 100%;
}

.column {
  display: flex;
  flex-direction: column;
  flex-basis: 100%;
  flex: 1;
}

.m20 {
  margin-left: 20px;
  margin-right: 20px;
}

.ml10 {
  margin-left: 10px;
}
</style>