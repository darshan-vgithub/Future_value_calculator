<template>
  <div class="pie-chart">
    <canvas ref="chartCanvas" width="300" height="300"></canvas>
  </div>
</template>

<script>
import Chart from "chart.js/auto";

export default {
  props: ["data"],
  mounted() {
    this.renderChart();
  },
  watch: {
    data: {
      handler() {
        this.renderChart();
      },
      deep: true,
    },
  },
  methods: {
    renderChart() {
      if (!this.data || !Array.isArray(this.data)) return;

      const ctx = this.$refs.chartCanvas.getContext("2d");
      const chartData = {
        labels: ["Invested Amount", "Wealth Gained"],
        datasets: [
          {
            backgroundColor: ["#FF6384", "#36A2EB"],
            data: this.data,
          },
        ],
      };
      const chartOptions = {
        responsive: true,
        maintainAspectRatio: true,
      };

      // Clear previous chart if exists
      if (this.chartInstance) {
        this.chartInstance.destroy();
      }

      // Render new chart
      this.chartInstance = new Chart(ctx, {
        type: "pie",
        data: chartData,
        options: chartOptions,
      });
    },
  },
};
</script>

<style scoped>
.pie-chart {
  margin-top: 30px;
  margin-bottom: 30px;
  width: 100%;
  height: 300px;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
