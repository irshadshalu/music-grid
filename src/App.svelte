<script>
	import Row from './Row.svelte'
	import {playRow} from './Music.svelte'
	let rows = 18;
	let columns = 12;
	let grid = [...Array(rows)].map(
		x => Array(columns).fill(false) );
	let playing = true;

	let curRow = 0;
	setInterval(() => {
		if(playing) {
			let nextRow = (curRow + 1) % rows;
			grid[curRow].isPlaying = false;
			grid[nextRow].isPlaying = true;
			curRow = nextRow;
			playRow(grid[curRow]);
		}
	}, 300);
</script>
<style>
	table {
		background: black;
	}

	.container {
		color:#DDD;
	}

</style>


<div class="container" align="center">
	<h3>Music Grid</h3>
	<br/>
	<br/>
	<table>
		{#each grid as row}
			<Row bind:row={row} bind:playing={row.isPlaying}/>
		{/each}
	</table>
	<br/>
	<br/>
	<label>
		<input type="checkbox" bind:checked={playing}> Play
	</label>
</div>