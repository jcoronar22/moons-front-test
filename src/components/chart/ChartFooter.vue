<template>
  <div class="row">
    <div v-for="(info, index) in chartInfo" :key="index">
      <chart-info-label
        :percentage="getPercentage(info.value)"
        :title="info.title"
        :quantity="valueWithUnit(info.value)"
        :color="info.color"
      />
    </div>
  </div>
</template>

<script>
import ChartInfoLabel from "./ChartInfoLabel.vue";

export default {
  name: "ChartFooter",
  props: {
    percentage: {
      type: Number,
      default: 0,
    },
    chartInfo: Array,
    unit: String,
  },
  computed: {
    total() {
      return this.chartInfo.reduce((a, b) => a.value + b.value);
    },
  },
  methods: {
    getPercentage(value) {
      return (value / this.total) * 100;
    },
    valueWithUnit(value) {
      return value.toLocaleString() + this.unit;
    },
  },
  components: { ChartInfoLabel },
};
</script>