<script>
	import Row from './Row.svelte'
	import {playRow} from './Music.svelte'

	let rows = 18;
	let columns = 12;
	let grid = [...Array(rows)].map(
		x => Array(columns).fill(false) );
	let playing = true;
	let speed = 200;
	let gameInterval;

	let curRow = 0;
	const reset = () => {
		grid = [...Array(rows)].map(
			x => Array(columns).fill(false) );
	}

	const changeSpeed = (bpm) => {
		clearInterval(gameInterval);
		gameInterval = setInterval(() => {
			if(playing) {
				let nextRow = (curRow + 1) % rows;
				grid[curRow].isPlaying = false;
				grid[nextRow].isPlaying = true;
				curRow = nextRow;
				playRow(grid[curRow]);
			}
		},  60*1000 / bpm);
	}

	$: changeSpeed(speed);
</script>
<style>
	table {
		background: black;
	}

	.container {
		color: #ddd;
	}

	.header a{
		font-size: 0.8em;
		color: #ccc;
	}
</style>


<div class="container" align="center">
	<div class="header" align="center">
		<a href="https://svelte.dev">Made  in Svelte ♥</a> | <a href="https://github.com/irshadshalu/music-grid">Source (GitHub)</a> | 
		<a href="https://irshadpi.me" >© irshadpi.me</a>
	</div>
	<h3>Music Grid</h3>
	<h5>Turn on sound. Tap on the grid. You'll figure it out ;)</h5>
	<br/>
	<br/>
	<table>
		{#each grid as row}
			<Row bind:row={row} bind:playing={row.isPlaying}/>
		{/each}
	</table>
	<br/>
	<label>
		Speed : {speed} bpm 
		<br/>
		<input bind:value={speed} type="range" min="60" max="400" class="slider">
	</label>
		<br/>
	<button on:click={() => playing = !playing}> 
		{ playing ? "Pause" : "Play" }
	</button>&nbsp;&nbsp;&nbsp;&nbsp;
	<button on:click={reset}>Clear</button>
</div>
