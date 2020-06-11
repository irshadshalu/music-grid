<script>
	import Cell from './Cell.svelte'
	export let row = [];
	export let playing = false;
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
	{#each row as cell}
		<Cell bind:active={cell}></Cell>
	{/each}
</tr>