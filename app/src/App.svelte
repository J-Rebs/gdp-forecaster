<script>
	import * as d3 from "d3";
	import { onMount } from "svelte";
	import { LayerCake, Svg } from "layercake";
	import * as aq from "arquero";
	//import * as aq from 'arquero';

	/**
	 * Line Chart Components
	 * Source: https://layercake.graphics/example/Line
	 * */
	import Line from "./lineChart/Line.svelte";
	import Area from "./lineChart/Area.svelte";
	import AxisXl from "./lineChart/AxisXL.svelte";
	import AxisYl from "./lineChart/AxisYL.svelte";

	let data;
	let container;

	const yKey = "WB_ny_gdp_mktp_cn_ad";
	const xKey = "year";
	/**
	 * on mount permits loading of async data with later access
	 * See: https://tuyukun.com/post/svelte_importdata/
	 */
	onMount(async () => {
		//load and transform data
		data = await d3.csv(
			"https://raw.githubusercontent.com/J-Rebs/gdp-forecaster/main/data/country_forecast.csv"
		);
		data.forEach((e) => {
			if (e[yKey] == "NA") {
				e[yKey] = 0;
			} else {
				e[yKey] = parseFloat(e[yKey]);
			}
		});

		//we also to create our arquero table
		const dt = aq.from(data);
		const child = document.createElement("div");
		child.innerHTML = dt.toHTML();
		container.appendChild(child);
	});

	$: {
		console.log(data);
	}
</script>

<main>
	<h1>Rapid Data Vizualization</h1>
	<p>
		Rapid data visualization of csv data using <a
			href="https://layercake.graphics/">layer cake</a
		>,
		<a href="https://observablehq.com/@uwdata/arquero">arquero</a>,
		<a href="https://d3js.org/">d3</a>, and
		<a href="https://svelte.dev/">svelte</a>
	</p>
</main>

<div class="chart-container">
	<LayerCake
		padding={{ right: 10, bottom: 20, left: 25 }}
		x={xKey}
		y={yKey}
		yDomain={[0, null]}
		{data}
	>
		<Svg>
			<AxisXl />
			<AxisYl ticks={4} />
			<Line />
			<Area />
		</Svg>
	</LayerCake>
</div>
<div bind:this={container}/>

<style>
	main {
		text-align: center;
		padding: 1em;
		max-width: 240px;
		margin: 0 auto;
	}

	h1 {
		color: #000000;
		text-transform: uppercase;
		font-size: 2em;
		font-weight: 100;
	}

	.chart-container {
		width: 100%;
		height: 500px;
		_background-color: rgba(0, 0, 0, 0.1);
		float: left;
	}

	@media (min-width: 640px) {
		main {
			max-width: none;
		}
	}
</style>
