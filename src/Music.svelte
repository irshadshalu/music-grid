<script context='module'>
	import * as Tone from 'tone';
	import bufferToWav from 'audiobuffer-to-wav';

	let synth;
	let recorder;
	let destinationStream;

	let scales = {
		classic: [
		  // first two notes not in early compositions
			'E3', 'G#3',
			'B3', 'C#4', 'F#4', 'G#4',
			'C#5', 'D#5', 'E5', 'G#5',
			'B5', 'C#6', 'F#6', 'G#6'
		],
		pentatonic: [
		  'G3', 'A3',
			'C4', 'D4', 'E4', 'G4',
			'A4', 'C5', 'D5', 'E5',
			'G5', 'A5', 'C6', 'D6',
		],
		chromatic: [
			'C5', 'C#5', 'D5', 'Eb5',
			'E5', 'F5', 'F#5', 'G5',
			'G#5','A5', 'Bb5', 'B5',
			'C6', 'C#6',
		],
		major: [
			'C4', 'D4', 'E4', 'F4',
			'G4', 'A4', 'B4', 'C5',
			'D5', 'E5', 'F5', 'G5',
			'A5', 'B5',
		],
		harmonic_minor: [
			'A4', 'B4', 'C5', 'D5',
			'E5', 'F5', 'G#5', 'A5',
			'B5', 'C6', 'D6', 'E6',
			'F6', 'G#6',
		],
	};

	let currentScale = scales['classic'];

	export const scale_keys = Object.keys(scales);

	export const initAudio = async () => {
		destinationStream  = Tone.context.createMediaStreamDestination();

		synth = new Tone.PolySynth(4, Tone.Synth, {
			oscillator : {
				type : 'triangle'
			}
		}).toMaster();
		synth.connect(destinationStream);

		synth.set('detune', -1200);

		await Tone.start();
		await Tone.context.resume();
		console.log('audio is ready');
	}

	export const setScale = key => {
		currentScale = scales[key];
	}

	export const playRow = async (row) => {
		if(!synth) {
			await initAudio();
		}

		let notesToPlay = []
		for (var i = row.length - 1; i >= 0; i--) {
			if(row[i]) {
				notesToPlay.push(currentScale[i]);
			}
		}
		synth.triggerAttackRelease(notesToPlay, '16n');
	}

	export const playCell = async (index) => {
		if(!synth) {
			await initAudio();
		}
		synth.triggerAttackRelease(currentScale[index], '16n');
	}

	export const stopRecording = async () => {
		if(recorder) {
			recorder.stop();
		}
	}

	export const startRecording = async (downloadLink) => {
		if(!destinationStream) {
			await initAudio();
		}

		recorder = new MediaRecorder(destinationStream.stream);

		let audioChunks = [];
		recorder.ondataavailable = async (e) => {
			let arrayBuffer = await e.data.arrayBuffer();
			Tone.context.decodeAudioData(arrayBuffer, (buffer) => {
				let wav = bufferToWav(buffer);
				let blob = new Blob([ new DataView(wav) ], {
					type: 'audio/wav'
				})
				downloadLink.href = URL.createObjectURL(blob);
				downloadLink.click();
			});
		}
		recorder.start();
	}
</script>
