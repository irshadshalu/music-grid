<script>
	import Cell from './Cell.svelte'
	export let row = [];
	export let playing = false;
	export let paused = false;
</script>

<style>
	tr.playing {
		transform: scale(0.95);
	}
	tr {
		transition: 0.1s ease-in all;
	}

</style>


<tr class:playing={playing}>
	{#each row as cell, index}
		<Cell bind:active={cell} column="{index}" paused={paused}></Cell>
	{/each}
</tr>