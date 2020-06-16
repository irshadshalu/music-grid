<script>
	import {playCell} from './Music.svelte'

	export let active = false;
	export let column = 0;

	const drawActive = (e) => {
		if(e.buttons) {
			toggleActive()
		}
	}

	const toggleActive = () => {
		active = !active;
		playCell(column);
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
		background: #444;
		border-radius: 3px;
		width: 1.5em;
		height: 1.5em;
		transition: 0.1s ease-in;
		user-drag: none; 
		user-select: none;
	}

	div.active {
		background: #fff;
		box-shadow: 0px 0px 8px 2px rgb(255, 255, 255, 0.5);
	}

</style>

<td>
	<div class:active={active} on:pointerover|capture={drawActive} on:click={toggleActive} >
	</div>
</td>