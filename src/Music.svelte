<script context='module'>
	import * as Tone from 'tone';
	import bufferToWav from 'audiobuffer-to-wav';

	let synth;
	let recorder;
	let destinationStream;

	let notes = [
		'B3', 'C#4', 'F#4', 'G#4',
		'C#5', 'D#5', 'E5', 'G#5',
		'B5', 'C#6', 'F#6', 'G#6'
	];

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

	export const playRow = async (row) => {
		if(!synth) {
			await initAudio();
		}

		let notesToPlay = []
		for (var i = row.length - 1; i >= 0; i--) {
			if(row[i]) {
				notesToPlay.push(notes[i]);
			}
		}
		synth.triggerAttackRelease(notesToPlay, '16n');
	}

	export const playCell = async (index) => {
		if(!synth) {
			await initAudio();
		}
		synth.triggerAttackRelease(notes[index], '16n');
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
