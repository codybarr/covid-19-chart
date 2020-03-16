// H1N1 Data: https://www.ncbi.nlm.nih.gov/pubmed/21342903
// COVID-19 Data: https://covidtracking.com/api/us/daily

<template>
  <div id="app">
    <h1>COVID-19 Chart</h1>
    <GChart
      class="chart"
      :settings="{ packages: ['corechart', 'line'] }"
      type="LineChart"
      :data="covidData"
      :options="chartOptions"
    />
  </div>
</template>

<script>
import moment from "moment";

export default {
  name: "App",
  data() {
    return {
      covidData: [["Day", "Positive Infections", "Deaths"]],
      chartOptions: {
        chart: {
          title: "USA COVID-19 Infections / Deaths"
        },
        width: "100%",
        height: 500,
        hAxis: {
          format: "MMM d"
        }
      }
    };
  },
  created() {
    this.fetchData();
  },
  methods: {
    async fetchData() {
      const response = await fetch(`https://covidtracking.com/api/us/daily`);
      const data = await response.json();

      const rows = data.map(day => {
        return [moment(day.date, "YYYYMMDD").toDate(), day.positive, day.death];
      });

      this.covidData = [["Day", "Positive Infections", "Deaths"], ...rows];
      console.log({ cdata: this.covidData });
    }
  }
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

.chart {
  width: 100%;
  min-height: 450px;
}
</style>
