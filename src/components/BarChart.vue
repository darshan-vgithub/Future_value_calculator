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
  mounted() {
    this.renderChart();
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
          indexAxis: "x",
          responsive: true,
          plugins: {
            legend: {
              display: false,
            },
            title: {
              display: true,
              text: "Investment and Wealth Gained",
            },
          },
          scales: {
            x: {
              beginAtZero: true,
            },
            y: {
              beginAtZero: true,
            },
          },
        },
      });
    },
  },
  watch: {
    data: {
      handler(newData) {
        if (this.chart) {
          this.chart.data.datasets[0].data = newData;
          this.chart.update();
        } else {
          this.renderChart();
        }
      },
      deep: true,
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
