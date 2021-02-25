<template>
  <div>
    <svg :id="svgId" :width="width" :height="height">
      <g :id="mainChartId">
        <text class="chart-header">
          <tspan class="chart-title" dy="-30">
            {{ capitalizedChartTitle }}
          </tspan>
          <tspan class="chart-total" x="0" dy="30">{{ calculateTotal }}</tspan>
        </text>
        <clipPath :id="clipPathId">
          <circle cx="0" cy="0" :r="marginInternalRadius" />
        </clipPath>
      </g>
    </svg>
    <chart-footer
      :chart-info="chartData.donutChartValues"
      :unit="chartData.unit"
    />
  </div>
</template>

<script>
import ChartFooter from "./ChartFooter";
import * as d3 from "d3";

export default {
  name: "Chart",
  props: {
    chartId: {
      type: String,
      required: true,
      default: "",
    },
    chartTitle: String,
    width: Number,
    height: Number,
    margin: Number,
    chartData: {
      type: Object,
      default: () => ({ donutChartValues: [] }),
    },
  },
  data() {
    return {
      cData: { donutChartValues: [] },
      donutValues: { max: 0, min: 0 },
    };
  },
  mounted() {
    this.cData = this.chartData;
    this.mapValues();
    this.generateChart();
  },
  computed: {
    calculateTotal: function () {
      return (
        Object.values(this.donutValues)
          ?.reduce((a, b) => a + b)
          .toLocaleString() + this.chartData.unit
      );
    },
    radius: function () {
      return Math.min(this.width, this.height) / 2 - this.margin;
    },
    internalRadius: function () {
      return this.radius - this.radius * 0.05;
    },
    marginInternalRadius: function () {
      return this.internalRadius - 10;
    },
    clipPathId: function () {
      return `clip-path-${this.chartId}`;
    },
    svgId: function () {
      return `svg-${this.chartId}`;
    },
    mainChartId: function () {
      return `main-${this.chartId}`;
    },
    capitalizedChartTitle: function () {
      return this.chartTitle.toUpperCase();
    },
  },
  methods: {
    generateChart() {
      const parsedHistoric = this.parseHistoric();

      let color = d3
        .scaleOrdinal()
        .domain(Object.keys(this.donutValues))
        .range(this.cData.colors);

      let pie = d3.pie().value((d) => d[1]);

      let dataReady = pie(Object.entries(this.donutValues));
      let arc = d3
        .arc()
        .innerRadius(this.internalRadius)
        .outerRadius(this.radius);

      let x = d3
        .scaleTime()
        .domain(
          d3.extent(parsedHistoric, function (d) {
            return d.date;
          })
        )
        .range([0, this.width]);

      let y = d3
        .scaleLinear()
        .domain([
          0,
          d3.max(parsedHistoric, function (d) {
            return +d.value;
          }),
        ])
        .range([this.height, 0]);

      let area = d3
        .area()
        .x((d) => x(d.date))
        .y0(this.height)
        .y1((d) => y(d.value));

      let svg = d3
        .select(`#${this.mainChartId}`)
        .attr("transform", `translate(${this.width / 2}, ${this.height / 2})`);

      svg
        .append("path")
        .data(parsedHistoric)
        .classed("area", true)
        .attr("d", area)
        .attr(
          "transform",
          `translate(${-this.width / 4}, ${this.height / 10})`
        );

      svg
        .append("g")
        .attr("id", `linear-${this.chartId}`)
        .attr("clip-path", `url(#${this.clipPathId})`)
        .append("path")
        .datum(parsedHistoric)
        .attr("stroke", this.chartData.colors[0])
        .attr("stroke-width", 2)
        .attr("opacity", 0.3)
        .attr("fill", "none")
        .attr("transform", `translate(${-this.width / 4}, ${this.height / 10})`)
        .attr(
          "d",
          d3
            .line()
            .x((d) => x(d.date))
            .y((d) => y(d.value))
            .curve(d3.curveBasis)
        );

      svg
        .append("g")
        .attr("id", `donut-${this.chartId}`)
        .selectAll(`#donut-${this.chartId}`)
        .data(dataReady)
        .enter()
        .append("path")
        .attr("d", arc)
        .attr("fill", (d) => color(d.data[0]))
        .attr("stroke", (d) => color(d.data[0]))
        .attr("class", "donut-chart");
    },
    parseHistoric() {
      return this.cData.historic.map((p) => {
        return {
          date: d3.timeParse("%Y-%m-%d")(p.date),
          value: p.value,
        };
      });
    },
    mapValues() {
      let aux = [];

      if (this.chartData.donutChartValues === undefined) return;

      this.chartData.donutChartValues.forEach((e) => {
        aux.push(e.value);
      });
      this.donutValues = {
        max: Math.max(...aux),
        min: Math.min(...aux),
      };
    },
  },
  components: { ChartFooter },
};
</script>

<style>
.chart-header {
  text-anchor: middle;
}

.chart-title {
  font-size: 20px;
  font-weight: bold;
  fill: grey;
  opacity: 0.5;
}

.chart-total {
  font-size: 28px;
  font-weight: bolder;
}

.area {
  fill: lightsteelblue;
  stroke-width: 1px;
}

.donut-chart {
  stroke-width: "1px";
  opacity: 0.9;
}
</style>