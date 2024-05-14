<template>
  <div>
    <div>
      <div class="nav">
        <span :class="{ active: calcType === 'future_value' }">
          <a href="#future_value">Future Value Calculator</a>
        </span>
        <span :class="{ active: calcType === 'expected_rate' }">
          <a href="#expected_rate">Expected Rate of Return Calculator</a>
        </span>
        <span :class="{ active: calcType === 'expected_period' }">
          <a href="#expected_period">Expected Period Calculator</a>
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
          <label for="monthly-investment">Monthly Investment</label>
          <input type="number" v-model="monthly_investment" />
        </div>
        <div>
          <label for="quarterly-investment">Quarterly Investment</label>
          <input type="number" v-model="quarterly_investment" />
        </div>
        <div>
          <label for="yearly-investment">Yearly Investment</label>
          <input type="number" v-model="yearly_investment" />
        </div>
        <div>
          <label for="duration">Duration (in months)</label>
          <input
            type="number"
            v-model="duration"
            :readonly="calcType === 'expected_period'"
          />
        </div>
        <div>
          <label for="rate">Rate</label>
          <input
            type="number"
            v-model="rate"
            :readonly="calcType === 'expected_rate'"
          />
        </div>
        <div>
          <label for="future-value">Future Value</label>
          <input
            type="number"
            v-model="futureValue"
            :readonly="calcType === 'future_value'"
          />
        </div>

        <br />
        <hr />
        <br />
        <p style="text-align: center">Wealth Gained: {{ wealthGained }}</p>
      </div>
      <div class="right-panel">
        <!-- <PieChart :data="chartData" /> -->
        <BarChart :data="chartData" />
      </div>
    </div>
  </div>
</template>

<script>
import BarChart from "./BarChart.vue";
import { Finance } from "financejs";

export default {
  name: "FutureValueCalculator",
  components: {
    BarChart,
  },
  data() {
    return {
      rate: 9.76, // Initial rate in percentage
      initialInvestment: 1000000,
      duration: 60,
      futureValue: 0,
      calcType: "future_value",
      monthly_investment: 10000,
      quarterly_investment: 0,
      yearly_investment: 50000,
      expected_return: 0,
    };
  },
  computed: {
    wealthGained() {
      if (!this.futureValue || !this.initialInvestment) return null;

      return (this.futureValue - this.initialInvestment).toFixed(2);
    },
    chartData() {
      return [this.initialInvestment, parseFloat(this.wealthGained)];
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
      // Create an array of cash flows for the IRR calculation
      const cashFlows = [];
      cashFlows.push(-this.initialInvestment); // Initial investment is negative
      for (let i = 1; i <= 20; i++) {
        // Calculate cash flows for each period
        const cashFlow =
          -this.monthly_investment * 12 +
          -this.yearly_investment +
          -this.quarterly_investment * 4;
        cashFlows.push(cashFlow);
      }
      cashFlows[cashFlows.length - 1] += this.futureValue; // Add future value as positive cash flow at the end

      // Ensure there are both positive and negative cash flows for IRR calculation
      if (
        cashFlows.every((value) => value >= 0) ||
        cashFlows.every((value) => value <= 0)
      ) {
        // If all cash flows are positive or all are negative, adjust the last one
        cashFlows[cashFlows.length - 1] -= this.futureValue;
      }

      // Calculate IRR using Finance library
      const finance = new Finance();
      this.expected_return = finance.IRR(cashFlows) * 100; // Multiply by 100 to get percentage
    },

    calc_expected_period() {
      const expected_return_rate = this.expected_return / 12;
      const yearly_investment_rate = -this.yearly_investment / 12;
      const quarterly_investment_rate = -this.quarterly_investment / 3;
      const base_expectation =
        this.nper(
          expected_return_rate,
          yearly_investment_rate +
            quarterly_investment_rate +
            -this.monthly_investment,
          -this.initialInvestment,
          this.futureValue
        ) / 12;
      return base_expectation;
    },
    nper(rate, payment, present_value, future_value) {
      // Calculate the number of periods
      const num_periods =
        Math.log(
          (payment - rate * future_value) / (payment + rate * present_value)
        ) / Math.log(1 + rate);
      return num_periods;
    },

    refresh_state() {
      const route = window.location.hash;
      this.calcType = route.replace("#", "");
      if (
        this.calcType === "expected_rate" ||
        this.calcType === "expected_period"
      ) {
        // Update the hash in the URL to reflect the current calculator type
        window.location.hash = this.calcType;
      }
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
