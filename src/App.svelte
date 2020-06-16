<script>
	import Row from './Row.svelte';
	import ClipboardJS from 'clipboard';
	import {initAudio, playRow} from './Music.svelte';

	let rows = 16;
	let columns = 12;
	let grid = [];
	let playing = false;
	let speed = 200;
	let gameInterval;
	let curRow = 0;
	let lastRow = 0;
	let shareMessage = 'Share';
	let started = false;
	let urlUpdatedRecently = false;

	const togglePlaying = async () => {
		playing = !playing;
		if(!started) {
			started = true;
			await initAudio();
		}
	}

	const stopPlaying = () => {
		playing = false;
		if(lastRow < grid.length) {
			grid[lastRow].isPlaying = false;
		}
		if(curRow < grid.length) {
			grid[curRow].isPlaying = false;
		}
		curRow = 0;
		lastRow = 0;
	}

	const clearGrid = (rows) => {
		curRow = 0;
		grid = [...Array(rows)].map(
			x => Array(columns).fill(false) );
	}

	const resizeGrid = (rows) => {
		stopPlaying();
		while(grid.length < rows) {
			grid.push([...Array(columns).fill(false)]);
		}

		while(grid.length > rows) {
			grid.pop();
		}

		grid[0].isPlaying = false;
	}

	const initGrid = (hash) => {
		playing = false;
		let array = hash.split('&')[0].slice(1).split('-').map(x => parseInt(x, 10));
		rows = array.length - 1;
		grid = []
		for (var i = array.length - 2; i >= 0; i--) {
			let temp = [... Array(columns).fill(false)];
			for (var j = columns - 1; j >= 0; j--) {
				temp[j] = (array[i] & (1 << (j))) !== 0;
			}
			grid.push(temp.reverse());
		}
		if(hash.split('&').length > 1) {
			speed = parseInt(hash.split('&')[1], 10);
		}
	}

	new ClipboardJS('.share', {
		text: function() {
			encodeGridToUrl(grid);
			shareMessage = 'Link copied';
			setTimeout(function() {
				shareMessage = 'Share';
			}, 3000);
			return window.location.href;
		}
	});

	const changeSpeed = (bpm) => {
		clearInterval(gameInterval);
		gameInterval = setInterval(() => {
			if(playing) {
				grid[lastRow].isPlaying = false;
				grid[curRow].isPlaying = true;
				playRow(grid[curRow]);
				lastRow  = curRow;
				curRow = (curRow + 1) % grid.length;
			}
		},  60*1000 / bpm);

		updateUrl(grid);
	}

	const encodeGridToUrl = (grid) => {
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

	const updateUrl = (grid) => {
		if(!urlUpdatedRecently) {
			encodeGridToUrl(grid);
			urlUpdatedRecently = true;
			setTimeout(() => {urlUpdatedRecently = false}, 1000);
		}
	}

	$: changeSpeed(speed);

	$: updateUrl(grid);

	clearGrid(rows);

	if(window.location.hash !== '') {
		initGrid(window.location.hash);
	}

</script>
<style>
	table {
		background: black;
	}

	.container {
		color: #ddd;
	}

	.footer a{
		font-size: 0.8em;
		color: #ccc;
		text-decoration: underline;
	}

	.tagline {
		font-size: 0.8em;
		font-weight: 600;
	}

	.message {
		font-size: 0.7em;
	}
	input[type=range] {
		width: 20em;
	}
</style>


<div class="container" align="center">
	<h3>Music Grid</h3>
	<span class="tagline">Turn on sound. Tap on the grid. Hit Play button. You'll figure it out ;)</span>
	<br/><br/>
	<label>
		Rows : {rows}<br/>
		<input bind:value={rows} on:input={() => resizeGrid(rows)} type="range" min="10" max="100" class="slider">
	</label>
	<button on:click={togglePlaying}> 
		{ playing ? "Pause" : "Play" }
	</button>&nbsp;&nbsp;&nbsp;&nbsp;
	<button on:click={stopPlaying}>Stop</button>&nbsp;&nbsp;&nbsp;&nbsp;
	<button on:click={() => clearGrid(rows)}>Clear</button>&nbsp;&nbsp;&nbsp;&nbsp;
	<button class="share">{shareMessage}</button>
	<table>
		{#each grid as row}
			<Row bind:row={row} bind:playing={row.isPlaying} paused={!playing}/>
		{/each}
	</table>
	<br/>
	<label>
		Speed : {speed} bpm 
		<br/>
		<input bind:value={speed} type="range" min="60" max="500" class="slider">
	</label>
	<br/>
	<div class="footer" align="center">
		<a href="https://irshadpi.me/2020-06-15/best-of-music-grid" >Best of Music Grid</a>
		<br/>
		<a href="https://svelte.dev">Made with Svelte</a> | <a href="https://github.com/irshadshalu/music-grid">Source (GitHub)</a> |
		<a href="https://irshadpi.me" >irshadpi.me</a> 
	</div>
	<br/>
	<br/>
</div>
