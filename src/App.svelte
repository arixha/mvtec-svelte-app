<script>
	//Some components
	import Line from './components/charts/Line.svelte'
	import Area from './components/charts/Area.svelte'
	import Bars from './components/charts/Bars.svelte'
	import Multiline from './components/charts/Multiline.svelte'
	import Scatter from './components/charts/Scatter.svelte'
	import ScatterCanvas from './components/charts/ScatterCanvas.svelte'
	import Mapbox from './components/maps/Mapbox.svelte'
	import Map from './components/maps/Map.svelte'
	import Scroller from '@sveltejs/svelte-scroller'
  	import Sankey from './components/charts/Sankey.svelte'

	//Test data
	import emissionsData from './data/emissions.json';
	import world from './data/world.json';
	import cases from './data/covid-cases.json';
	import weather from './data/weather.json';
	import weather2 from './data/weather2.json';
	import weather3 from './data/weather3.json';
	import sankeydata from './data/sankey-data.js';
	import locale from '@reuters-graphics/d3-locale';
	import { geoWinkel3 } from 'd3-geo-projection';
	import { scaleQuantize } from 'd3-scale';
	import {groups,extent} from 'd3-array';

	let color = '#5cc6b2';
	let index = 0, offset, progress;
	let scatterStep, otherStep;
	const loc = new locale('es');

	const format = {
		x: loc.formatTime('%Y'),
        y: loc.format('d'),
    }

	weather.forEach(d => d.time = new Date(d.time));
	weather2.forEach(d => d.time = new Date(d.time));
	emissionsData.forEach(d => d.emissions = +d.emissions);
	emissionsData.forEach(d => d.year = new Date(d.year));

	const emissionsByCountry = groups(emissionsData, d => d.code)
		.map(d => (
			{code:d[0],
			 data: d[1],
			 latest:d[1][d[1].length - 1]})
		);

	const projection = geoWinkel3()
				.rotate([-11, 0])
				.precision(1);
	
	//console.log(cases)

	//console.log(emissionsData)
	//console.log(emissionsByCountry)

	const palette = () => {
		const _extent = extent(cases.data, d => d.latest.cases)
		const max = _extent[0] > _extent[1] ? _extent[0] : _extent[1];
		const min = _extent[0] < _extent[1] ? _extent[0] : _extent[1];
		const d = (max-min)/9;
		return scaleQuantize()
				.range(['#ffe461', '#ffc755', '#fea94d', '#f68c4a', '#ea704a', '#da554e', '#c73a55', '#ae1f5f', '#90006c'])
				.domain([... Array(9)].map((_d, i) => min + d * i))
				.nice();
	}

	const paletteEmissions = () => {
		const _extent = extent(emissionsData, d => d.emissions)
		const max = _extent[0] > _extent[1] ? _extent[0] : _extent[1];
		const min = _extent[0] < _extent[1] ? _extent[0] : _extent[1];
		const d = (max-min)/9;
		return scaleQuantize()
				.range(['#ffe461', '#ffc755', '#fea94d', '#f68c4a', '#ea704a', '#da554e', '#c73a55', '#ae1f5f', '#90006c'])
				.domain([... Array(9)].map((_d, i) => min + d * i))
				.nice();
	}


	const steps = [
		{center: [-7.889722,42.782222], zoom:7.5},
		{center: [0,42.782222], zoom:7},
		{center: [-7.889722,41.782222], zoom:6.5}
	]


	const points = [...new Array(2500)]
        .map(d => (
            {
				coords: [
					{ x: Math.random() * 400,
					y: Math.random() * 400,
					width: Math.random() * 16,
					height: Math.random() * 16 },
					{ x: Math.random() * 400,
					y: Math.random() * 400,
					width: Math.random() * 16,
					height: Math.random() * 16 }
				]
        }))
	const otherPoints = [...new Array(800)]
        .map(d => (
            {
				coords: [
					{ x: Math.random() * 400,
					y: Math.random() * 400,
					r: Math.random() * 16 },
					{ x: Math.random() * 400,
					y: Math.random() * 400,
					r: Math.random() * 16 }
				]
        }))
	
</script>

<main>

	<!-- <Multiline 
		data={weather2}
		title='Title' desc='Description'
		key={{x: 'time', y: ['ny1', 'sf1', 'au1']}}
		{format}
		color={['#fc0', '#036', '#f0c']}
		layout='col'
	/> -->

	

	<!-- <Map 
		data={cases.data}
		map={world}
		geo='countries'
		scale={palette()}
		projection={projection}
    	join={{data:'geoid', map:'alpha3'}}
    	value='latest.cases'
    	legend={{title: '', format: ''}}
		layout='wide'
	/> -->


	<Map 
		data={emissionsByCountry}
		map={world}
		geo='countries'
		scale={paletteEmissions()}
		projection={projection}
    	join={{data:'code', map:'alpha3'}}
    	value='data[28].emissions'
    	legend={{title: '', format: ''}}
		layout='wide'
	/>
	
</main>

<style>
	main {
		padding: 1em;
		margin: 0 auto;
	}
	:global(.graphic) {
		height:50vh;
		margin-bottom:3rem;
	}
	
	[slot="foreground"] {
		pointer-events: none;
	}
	
	[slot="foreground"] section {
		pointer-events: all;
	}
	
	section {
		height: 80vh;
		padding: 1em;
		margin: 0 0 2em 0;
	}
</style>