<template>
  <div class="footer-container column">
    <div class="row">
      <div class="column">
        <p class="footer-title" :style="colorStyle">{{ title }}</p>
      </div>
    </div>
    <div class="row mt0 mr15">
      <div class="column">
        <p class="percentage">{{ percentage }}%</p>
      </div>
      <div class="column mr25">
        <p class="qty">{{ quantity }}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "ChartInfoLabel",
  props: {
    title: String,
    percentage: Number,
    quantity: String,
    color: String,
  },
  computed: {
    colorStyle: function () {
      return `color: ${this.color}`;
    },
  },
};
</script>

<style>
.footer-container {
  margin-left: 0px;
}

.no-margin {
  margin: 0, 0, 0, 0;
}

.footer-title {
  font-weight: bold;
  font-size: 15px;
}

.mt0 {
  margin-top: -25px;
}

.mr15 {
  margin-right: 15px;
}

.mr25 {
  margin-right: 25px;
}

.percentage {
  font-weight: bold;
  font-size: 15px;
}

.qty {
  color: grey;
  opacity: 0.5;
  font-weight: bold;
}
</style>