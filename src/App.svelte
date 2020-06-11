<script>
	import Row from './Row.svelte'
	let rows = 15;
	let columns = 7;
	let grid = [...Array(rows)].map(
		x => Array(columns).fill(false) );

	let curRow = 0;
	setInterval(() => {
		let nextRow = (curRow + 1) % rows;
		grid[curRow].isPlaying = false;
		grid[nextRow].isPlaying = true;
		curRow = nextRow;
	}, 300);
</script>
<style>
	table {
		background: black;
	}

	h3 {
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
</div>