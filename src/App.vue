// H1N1 Data: https://www.ncbi.nlm.nih.gov/pubmed/21342903
// COVID-19 Data: https://covidtracking.com/api/us/daily

<template>
  <div id="app">
    <h1>
      COVID-19 vs.
      <a href="https://www.ncbi.nlm.nih.gov/pubmed/21342903">H1N1</a>
    </h1>
    <fieldset>
      <legend>Select Chart:</legend>
      <label>
        COVID-19
        <input type="radio" name="covid19" value="covid19" v-model="selectedRadio" checked>
      </label>
      <label>
        H1N1
        <input type="radio" name="h1n1" value="h1n1" v-model="selectedRadio">
      </label>
      <label>
        COVID-19 vs H1N1
        <input type="radio" name="both" value="both" v-model="selectedRadio">
      </label>
    </fieldset>
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
      covidData: [
        [
          "Day",
          "COVID Infections",
          "COVID Deaths",
          "H1N1 Infections",
          "H1N1 Deaths"
        ]
      ],
      chartOptions: {
        chart: {
          title: "USA COVID-19 Infections / Deaths"
        },
        width: "100%",
        height: 500,
        hAxis: {
          format: "MMM d"
        }
      },
      selectedRadio: "covid19"
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
        return [
          moment(day.date, "YYYYMMDD").toDate(),
          day.positive,
          day.death,
          60800000,
          12000
        ];
      });

      this.covidData = [
        [
          "Day",
          "Positive Infections",
          "Deaths",
          "H1N1 Infections",
          "H1N1 Deaths"
        ],
        ...rows
      ];
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

label {
  display: inline-block;
}

label:not(:first-child) {
  margin-left: 1rem;
}

/* fieldset {
  display: flex;
  flex-direction: column;
} */
</style>
