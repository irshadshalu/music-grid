<script>
	import { createEventDispatcher, onMount } from 'svelte';
	import ClipboardJS from 'clipboard';

	const dispatch = createEventDispatcher();

	export let grid;
	export let config;

	let urlUpdatedRecently = false;
	let settingsOpen = false;
	let scrollY = 0;

	const encodeGridToUrl = (grid, speed) => {
		let res = ''
		for (var i = grid.length - 1; i >= 0; i--) {
			let temp = 0, k = 1;
			for (var j = grid[i].length - 1; j >= 0; j--) {
				temp = temp + k * grid[i][j];
				k = k * 2;
			}
			res += (temp + '-');
		}
		history.replaceState({}, '', '#' + res + '&' + speed);
	}

	const updateUrl = (grid, speed) => {
		if(!urlUpdatedRecently) {
			encodeGridToUrl(grid, speed);
			urlUpdatedRecently = true;
			setTimeout(() => {urlUpdatedRecently = false}, 1000);
		}
	}

	$: updateUrl(grid, config.speed);


	let clipboard = new ClipboardJS('.share', {
		text: function() {
			encodeGridToUrl(grid, config.speed);
			return window.location.href;
		}
	});


	let header;
	let headerOffset;
	let primaryClass = "primary";

	onMount(() => {
		headerOffset = header.offsetTop;
	});

	const showSticky = (ignored) => {
		if(headerOffset) {
			if (window.pageYOffset > headerOffset) {
				primaryClass = "primary sticky";
			} else {
				primaryClass = "primary";
			}
		}
	}

	$: showSticky(scrollY);

</script>

<style>

	.fa {
		transition: 0.06s ease-in;
	}

	.fa:hover {
		transform: scale(1.1);
	}

	.fa:active {
		transform: scale(0.8);
	}

	a {
		margin-left: 1.3em;
		margin-right: 1.3em;
		padding: 10px;
		color: white;
	}

	.primary, .settings {
		padding: 10px;
		background: black;
	}

	.sticky {
		position: fixed;
		top: 0;
		left: 0;
		width: 100%;
	}
</style>
<svelte:window bind:scrollY={scrollY}/>

<div class="container">
	<div class="settings">
		<label>
			Rows :
			<input bind:value={config.rows}
			on:input={() => dispatch('rowchange')}
			 type="range" min="8" max="100" class="slider">
			 {config.rows}
		</label>
		<br/>
		<label>
			Speed :
			<input bind:value={config.speed}
			 type="range" min="60" max="500" class="slider">
			 {config.speed}
		</label>
	</div>
	<div class={primaryClass} bind:this={header}>
		<a on:click={() => dispatch('playpause')}>
			{#if config.playing}
				<i class="fa fa-lg fa-inverse fa-pause"/>
			{:else}
				<i class="fa fa-lg fa-inverse fa-play"/>
			{/if}
		</a>
		<a on:click={() => dispatch('stop')}><i class="fa fa-inverse fa-lg fa-stop"/></a>
		<a class="share"><i class="fa fa-lg fa-share-alt"/></a>
		<a on:click={() => dispatch('clear')}><i class="fa fa-lg fa-trash"/></a>
	</div>
	{#if settingsOpen}
	{/if}
</div>
