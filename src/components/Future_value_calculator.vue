<template>
  <div>
    <div>
      <div class="nav">
        <span :class="{ active: calcType == 'future_value' }">
          <a href="#future_value"> Future Value Calculator </a>
        </span>
        <span :class="{ active: calcType == 'expected_rate' }">
          <a href="#expected_rate"> Expected Rate of return calculator </a>
        </span>
        <span :class="{ active: calcType == 'expected_period' }">
          <a href="#expected_period"> Expected Period Calculator </a>
        </span>
      </div>
    </div>
    <div class="panel-container">
      <div class="left-panel">
        <hr />
        <br />
        <div>
          <label for="initial-investment">Initial investment</label>
          <input type="number" v-model="initialInvestment" />
        </div>
        <div>
          <label for="duration">Duration (in months)</label>
          <input
            type="number"
            v-model="duration"
            :readonly="calcType == 'expected_period'"
          />
        </div>
        <div>
          <label for="duration">Rate</label>
          <input
            type="number"
            v-model="rate"
            :readonly="calcType == 'expected_rate'"
          />
        </div>
        <div>
          <label for="duration">Future Value</label>
          <input
            type="number"
            v-model="futureValue"
            :readonly="calcType == 'future_value'"
          />
        </div>

        <br />
        <hr />
        <br />
        <p style="text-align: center">Wealth Gained: {{ WealthGained }}</p>
      </div>
      <div class="right-panel">
        <PieChart :data="chartData" />
      </div>
    </div>
  </div>
</template>

<style>
input[readonly] {
  background-color: #ddd;
}

.nav {
  text-align: center;
  margin: 10px 10px 120px 10px;
  padding-bottom: 10px;
  border-bottom: solid #ccc 1px;
}

.nav a {
  background-color: #fff;
  border: none;
  color: #449;
  border-right: solid #ddd 2px;
  padding: 5px 30px;
  cursor: pointer;
}

.nav span.active a {
  background-color: #eef;
  border: none;
  color: #44f;
  border-right: solid #ddd 2px;
  padding: 5px 30px;
}

.panel-container {
  margin-top: 40px;
  display: flex;
  justify-content: space-evenly;
  align-items: start;
}

.left-panel {
  padding-right: 30px;

  border-right: solid #eee 3px;
}

label {
  width: 300px;
  display: inline-block;
  margin-bottom: 10px;
}

input[type="number"] {
  width: 100px;
}
</style>

<script>
import PieChart from "./PieChart.vue";

export default {
  name: "future_value_calculator",
  components: {
    PieChart,
  },
  data() {
    return {
      rate: 9.76, // Initial rate in percentage
      initialInvestment: 1000000,
      duration: 60,
      futureValue: 0,
      calcType: "future_value",
    };
  },
  computed: {
    changedData() {
      const { rate, initialInvestment, duration, futureValue, calcType } = this;
      return {
        rate,
        initialInvestment,
        duration,
        futureValue,
        calcType,
      };
    },
    WealthGained() {
      if (!this.futureValue || !this.initialInvestment) return null;

      const wealthGained = this.futureValue - this.initialInvestment;
      return wealthGained.toFixed(2); // Return wealth gained with 2 decimal places
    },
    chartData() {
      return [this.initialInvestment, this.WealthGained];
    },
  },
  watch: {
    changedData: {
      handler(newValue, oldValue) {
        this.calculate();
      },
      deep: true,
    },
  },
  methods: {
    calculate() {
      switch (this.calcType) {
        case "future_value":
          this.calc_future_value();
          break;
        case "expected_rate":
          this.calc_expected_rate();
          break;
        case "expected_period":
          this.calc_expected_period();
          break;
      }
    },
    calc_future_value() {
      if (!this.initialInvestment || !this.duration) return null;

      const rateDecimal = this.rate / 100; // Convert rate to decimal
      const months = parseInt(this.duration); // Parse duration to integer
      const value =
        this.initialInvestment * Math.pow(1 + rateDecimal, months / 12);
      this.futureValue = value.toFixed(2); // Return future value with 2 decimal places
    },
    calc_expected_rate() {
      // TODO
    },
    calc_expected_period() {
      // TODO
    },
    refresh_state() {
      const route = window.location.hash;
      this.calcType = route.replace("#", "");
      this.calculate();
    },
  },
  mounted() {
    this.refresh_state();
    window.onhashchange = () => {
      this.refresh_state();
    };
  },
};
</script>
