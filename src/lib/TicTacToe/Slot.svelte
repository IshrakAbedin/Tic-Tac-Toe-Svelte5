<script>
	let { state = $bindable(), nextState, canMove, postMoveAction } = $props();

	let notAlreadySelected = $derived(state !== 'x' && state !== 'o');

	function applyMove() {
		if (canMove && notAlreadySelected) {
			state = nextState;
			postMoveAction();
		}
	}
</script>

<button class:xb={state === 'x'} class:ob={state === 'o'} onclick={applyMove}>
	{state}
</button>

<style>
	button {
		border-radius: 20%;
		border: 2px solid #494949;
		font-size: 1.65em;
		justify-content: center;
		transition: all 0.15s ease-in-out;
		size: 100%;
	}
	button:hover {
		border-radius: 25%;
		border: 2px solid #00b3ff;
	}
	button:active {
		border-radius: 50%;
		border: 2px dashed #1500ff;
		background-color: #1c0057;
	}

	.xb {
		color: #b100e7;
	}

	.ob {
		color: #eb5e00;
	}
</style>
