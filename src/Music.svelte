<script context="module">
	import * as Tone from "tone";

	let synth;

	let noteValues = [
		246.94, 277.18, 311.13, 369.99,
		392.00, 415.30, 466.16, 554.37,
		587.33, 622.25, 659.26, 739.99,
		830.61, 932.33, 987.77, 1046.50,
		1108.73, 1244.51, 1479.98, 1661.22,
		1864.66, 2093.00, 2217.46, 2637.02,
		2959.96, 3322.44, 3520.00, 3729.31
	];

	const rangeMap = (a, b) => s => {
		const [a1, a2] = a;
		const [b1, b2] = b;
		return (((((b2 - b1) * (s - a1)) / (a2 - a1)) * 10) + (10 * b1)) / 10;
	};

	const mapping = rangeMap([0, 4095], [200, 3000])

	export const initAudio = async () => {
		synth = new Tone.PolySynth(4, Tone.Synth, {
			oscillator : {
				type : "triangle"
			}
		}).toMaster();

		synth.set("detune", -1200);

		await Tone.start();
		await Tone.context.resume();
		console.log('audio is ready');
	}

	export const playRow = (row) => {
		if(!synth) {
			console.error("Please initialize audio before playing a row");
			return;
		}
		const rowValue = parseInt(row.map(x => x ? '1' : '0' ).reverse().join(''), 2);
		let notesToPlay = []
		for (var i = row.length - 1; i >= 0; i--) {
			if(row[i]) {
				notesToPlay.push(noteValues[Math.floor(i * 1.8)]);
			}
		}
		synth.triggerAttackRelease(notesToPlay, "16n");
	}

	export const playCell = (index) => {
		let notesToPlay = [noteValues[Math.floor(index * 1.8)]];
		synth.triggerAttackRelease(notesToPlay, "16n");
	}
</script>