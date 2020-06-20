<script context="module">
	import * as Tone from "tone";

	let synth;

	let notes = [
		'B3', 'C#4', 'F#4', 'G#4',
		'C#5', 'D#5', 'E5', 'G#5',
		'B5', 'C#6', 'F#6', 'G#6'
	];

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

	export const playRow = async (row) => {
		if(!synth) {
			console.error("Please initialize audio before playing a row");
			await initAudio();
			return;
		}

		let notesToPlay = []
		for (var i = row.length - 1; i >= 0; i--) {
			if(row[i]) {
				notesToPlay.push(notes[i]);
			}
		}
		synth.triggerAttackRelease(notesToPlay, "16n");
	}

	export const playCell = async (index) => {
		if(!synth) {
			await initAudio();
		}
		synth.triggerAttackRelease(notes[index], "16n");
	}
</script>