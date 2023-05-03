<template>
  <div>
    <canvas
      :id="id"
      width="200"
      height="200"
      style="max-height: 200px; max-width: 200px"
    ></canvas>
  </div>
</template>
<script>
import Chart from "chart.js";

export default {
  props: {
    chartType: { type: String, default: "" },
    chartData: { type: Object, default: null },
    chartOptions: { type: Object, default: null },
    chartText: { type: String, default: "" },
    id: { type: String, default: "doughnutChart" },
  },
  mounted() {
    this.chartConstructor(
      this.chartType,
      this.chartData,
      this.chartOptions,
      this.chartText
    );
  },
  data() {
    return {
      chart: null,
    };
  },
  watch: {
    chartData: function (newData) {
      this.chart.data.datasets.datasets = newData;
      this.chart.update();
      this.chartConstructor(
        this.chartType,
        this.chartData,
        this.chartOptions,
        this.chartText
      );
    },
  },
  methods: {
    chartConstructor(chartType, chartData, chartOptions) {
      const chartElement = document.getElementById(this.id);

      //Destroy the old instance of doughnut chart to prevent old data from being rendered.
      if (this.chart) this.destroyChart();

      this.chart = new Chart(chartElement, {
        type: chartType,
        data: chartData,
        options: chartOptions,
      });

      // this.chart.update();
    },
    updateChart() {
      this.chartConstructor(
        this.chartType,
        this.chartData,
        this.chartOptions,
        this.chartText
      );
    },
    destroyChart() {
      this.chart.destroy();
    },
  },
};
</script>
