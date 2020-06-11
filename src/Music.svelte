<script context="module">
	let context = new AudioContext();

    const rangeMap = (a, b) => s => {
      const [a1, a2] = a;
      const [b1, b2] = b;
      return (((((b2 - b1) * (s - a1)) / (a2 - a1)) * 10) + (10 * b1)) / 10;
    };

    const mapping = rangeMap([0, 4095], [200, 3000])

	const playWithFrequency = (frequency, type) => {
		let oscillator = context.createOscillator();
		let gain = context.createGain();
		oscillator.type = type
		oscillator.connect(gain)
		oscillator.frequency.value = frequency
		gain.connect(context.destination)
		oscillator.start(0)
		gain.gain.exponentialRampToValueAtTime(0.00001, context.currentTime + 1);
	}

	export const playRow = (row) => {
		const rowValue = parseInt(row.map(x => x ? '1' : '0' ).reverse().join(''), 2);
		if(rowValue !== 0) {
			playWithFrequency(mapping(rowValue), 'triangle');
		}
	}
</script>