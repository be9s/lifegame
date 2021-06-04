<template>
  <div id="chart-container">
    <canvas id="monitor"></canvas>
  </div>
</template>

<script>
import Chart from "chart.js/auto";

const data = {
  labels: Array(6).fill(""),
  datasets: [
    {
      data: Array(6).fill(0),
      fill: true,
      pointRadius: 0,
      borderWidth: 1,
      backgroundColor: "rgba(54, 162, 235, 0.2)",
      borderColor: "rgb(54, 162, 235)",
      cubicInterpolationMode: "monotone",
    },
  ],
};
const config = {
  type: "line",
  data,
  options: {
    events: [],
    plugins: {
      legend: { display: false },
    },
    scales: {
      y: {
        min: 0,
        max: 100,
        display: false,
      },
      x: {
        display: false,
      },
    },
  },
};
const updateChart = function(chart, val) {
  let data = chart.data.datasets[0].data;
  data.shift();
  data.push(val);
  chart.update();
};
export default {
  props: ["ratio"],
  data() {
    return {
      monitor: {},
    };
  },
  mounted() {
    this.monitor = new Chart(document.getElementById("monitor"), config);
  },
  watch: {
    ratio(val) {
      updateChart(this.monitor, val);
    },
  },
};
</script>
<style scoped>
#chart-container {
  width: 75px;
}
</style>
