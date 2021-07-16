<script>	
	import Line from '../../components/charts/Line.svelte'
	import locale from '@reuters-graphics/d3-locale';

	export let x;
	export let y;
	export let width;
	export let height;
	export let tip = 'No data available';
	export let visible = false;
    export let tooltipWidth = 400;
	$: tx = (x < width - tooltipWidth && x >= tooltipWidth / 2) 
		? x - tooltipWidth / 2
		: x < tooltipWidth / 2
		? 0
		: width - tooltipWidth - tooltipWidth / 2;
	
	$: ty = y + 30;

	$:console.log("hola tooltip ")
	$:console.log(tip)

	let color = '#5cc6b2';
	const loc = new locale('es');

	const format = {
		x: loc.formatTime('%Y'),
        y: loc.format('d'),
    }

</script>



<div class="tooltip" style="top: {ty}px; left: {tx+300}px; opacity: {visible ? 1: 0}; width: {tooltipWidth}px; height: 200px">
	<!-- {@html tip} -->
	{#if tip.data}
	<Line
		data={tip.data}
		title='Title' desc='Description'
		key={{x: 'year', y: 'emissions'}}
		{format}
		{color}
		layout='col'
	/>
	{/if}
</div>

<style>
	.tooltip {
		position:absolute;
		font-size: 0.75rem;
		background:rgba(255,255,255,.9);
        box-shadow: rgba(0,0,0,.2) 1px 1px 9px;
        padding:.5rem;
		transition: opacity .3s;
	}
</style>