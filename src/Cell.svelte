<script>
	export let active = false;
	export let playing = false;


	const toggleActive = () => {
		active = !active;
	}
</script>

<style>

	td {
		padding-top: 0.15em;
		padding-bottom: 0.15em;
		padding-left: 0.1em;
		padding-right: 0.1em;
	}
	
	div {
		background: #222;
		border-radius: 3px;
		width: 2em;
		height: 2em;
		transition: 0.1s ease-in;
	}

	div.active {
		background: #fff;
		box-shadow: 0px 0px 8px 2px rgb(255, 255, 255, 0.5);
	}

	div.playing {
		background: #f00;
		transform: scale(1.1);
	}

	div.active.playing {
		background: #fff;
		box-shadow: 0px 0px 10px 3px rgb(255, 255, 255, 0.8);
	}


</style>

<td>
	<div class:playing={playing} class:active={active} on:click={toggleActive}>
	</div>
</td>