<template>
  <canvas ref="barChart"></canvas>
</template>

<script>
import Chart from "chart.js/auto";

export default {
  name: "BarChart",
  props: {
    data: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      chart: null,
    };
  },
  mounted() {
    this.renderChart();
  },
  watch: {
    data: {
      handler(newData) {
        if (this.chart) {
          this.chart.data.datasets[0].data = newData;
          this.chart.update();
        }
      },
      deep: true,
    },
  },
  methods: {
    renderChart() {
      const ctx = this.$refs.barChart.getContext("2d");
      this.chart = new Chart(ctx, {
        type: "bar",
        data: {
          labels: ["Initial Investment", "Wealth Gained"],
          datasets: [
            {
              label: "Amount",
              data: this.data,
              backgroundColor: ["#A2D9D9", "#4CB140"],
              borderColor: ["rgba(75, 192, 192, 1)", "rgba(255, 99, 132, 1)"],
              borderWidth: 1,
            },
          ],
        },
        options: {
          scales: {
            y: {
              beginAtZero: true,
            },
          },
        },
      });
    },
  },
};
</script>

<style scoped>
canvas {
  max-width: 400px;
  max-height: 400px;
}
</style>
