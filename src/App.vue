<template>
	<div id="app">
		<header>
			<h1>
				<a href="https://covidtracking.com" target="_blank" rel="noreferrer noopener">COVID-19</a> vs.
				<a
					href="https://www.ncbi.nlm.nih.gov/pubmed/21342903"
					target="_blank"
					rel="noreferrer noopener"
				>H1N1</a>
			</h1>
			<h2>
				<em>US Data Only</em>
			</h2>
		</header>
		<main>
			<fieldset>
				<legend>Select Chart:</legend>
				<label>
					COVID-19
					<input type="radio" name="covid19" value="covid" v-model="selectedRadio" />
				</label>
				<label>
					H1N1
					<input type="radio" name="h1n1" value="h1n1" v-model="selectedRadio" />
				</label>
				<label>
					COVID-19 vs H1N1
					<input type="radio" name="both" value="both" v-model="selectedRadio" />
				</label>
			</fieldset>
			<GChart
				class="chart"
				:settings="{ packages: ['corechart', 'line'] }"
				type="LineChart"
				:data="rowData"
				:options="chartOptions"
			/>
		</main>
	</div>
</template>

<script>
// H1N1 Data: https://www.ncbi.nlm.nih.gov/pubmed/21342903
// COVID-19 Data: https://covidtracking.com/api/us/daily

import moment from 'moment'

export default {
	name: 'App',
	data() {
		return {
			chartOptions: {
				chart: {
					title: 'USA COVID-19 Infections / Deaths',
				},
				width: '100%',
				height: 500,
				hAxis: {
					format: 'MMM d',
				},
				tooltip: { isHtml: true },
			},
			selectedRadio: 'covid',
			covidData: [],
		}
	},
	created() {
		this.fetchData()
	},
	methods: {
		async fetchData() {
			const response = await fetch(
				`https://covidtracking.com/api/us/daily`
			)
			const data = await response.json()
			this.covidData = data
		},
	},
	computed: {
		rowData() {
			return this.data[this.selectedRadio]
		},
		data() {
			const covidRows = this.covidData.map(day => {
				return [
					moment(day.date, 'YYYYMMDD').toDate(),
					day.positive,
					`<ul class="google-visualization-tooltip-item-list"><li class="google-visualization-tooltip-item">COVID Infections: <strong>${day.positive}</strong></li><ul>`,
					day.death,
					`<ul class="google-visualization-tooltip-item-list"><li class="google-visualization-tooltip-item">COVID Deaths: <strong>${
						day.death
					} (${Math.round((day.death / day.positive) * 10000) /
						100}%)</strong></li><ul>`,
				]
			})

			const h1n1Rows = this.covidData.map(day => {
				return [moment(day.date, 'YYYYMMDD').toDate(), 60800000, 12000]
			})

			const bothRows = this.covidData.map(day => {
				return [
					moment(day.date, 'YYYYMMDD').toDate(),
					day.positive,
					day.death,
					60800000,
					12000,
				]
			})

			return {
				covid: [
					[
						'Day',
						'COVID Infections',
						{ type: 'string', role: 'tooltip', p: { html: true } },
						'COVID Deaths',
						{ type: 'string', role: 'tooltip', p: { html: true } },
					],
					...covidRows,
				],
				h1n1: [['Day', 'H1N1 Infections', 'H1N1 Deaths'], ...h1n1Rows],
				both: [
					[
						'Day',
						'COVID Infections',
						'COVID Deaths',
						'H1N1 Infections',
						'H1N1 Deaths',
					],
					...bothRows,
				],
			}
		},
	},
}
</script>

<style>
#app {
	font-family: 'Avenir', Helvetica, Arial, sans-serif;
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

.google-visualization-tooltip-item-list {
	font-size: 15px;
}
</style>
