<script>
	import Slot from '$lib/TicTacToe/Slot.svelte';

	let states = $state(Array(9).fill(''));
	let cachedStates = $state([]);
	let round = $state(0);

	$inspect(cachedStates);

	let nextMove = $derived(round % 2 ? 'o' : 'x');
	let winner = $derived(calculateWinner(states));

	function postMoveJob() {
		round += 1;
		cachedStates.push({
			round: round,
			states: [...states]
		});
		cachedStates = cachedStates;
	}

	function rewind(index) {
		states = [...cachedStates[index].states];
		round = cachedStates[index].round;
		cachedStates = cachedStates.slice(0, index + 1);
	}

	function reset() {
		states.fill('');
		cachedStates.length = 0;
		round = 0;
	}

	function calculateWinner(arr) {
		const minC = '15.5%';
		const midC = '50%';
		const maxC = '84.5%';
		const lineRules = [
			{ indices: [0, 1, 2], line: { x1: minC, y1: minC, x2: maxC, y2: minC } }, // Top row
			{ indices: [3, 4, 5], line: { x1: minC, y1: midC, x2: maxC, y2: midC } }, // Middle row
			{ indices: [6, 7, 8], line: { x1: minC, y1: maxC, x2: maxC, y2: maxC } }, // Bottom row
			{ indices: [0, 3, 6], line: { x1: minC, y1: minC, x2: minC, y2: maxC } }, // Left column
			{ indices: [1, 4, 7], line: { x1: midC, y1: minC, x2: midC, y2: maxC } }, // Middle column
			{ indices: [2, 5, 8], line: { x1: maxC, y1: minC, x2: maxC, y2: maxC } }, // Right column
			{ indices: [0, 4, 8], line: { x1: minC, y1: minC, x2: maxC, y2: maxC } }, // Diagonal 1
			{ indices: [2, 4, 6], line: { x1: minC, y1: maxC, x2: maxC, y2: minC } } // Diagonal 2
		];

		for (const rule of lineRules) {
			const [a, b, c] = rule.indices;
			if (arr[a] && arr[a] === arr[b] && arr[a] === arr[c]) {
				return { player: arr[a], line: rule.line };
			}
		}

		if (round === 9) return { player: 'draw', line: null };
		return null;
	}
</script>

<div class="hflex">
	<h2>Current Round: {round + 1}</h2>
	<h2>|</h2>
	<h2>Next Move is '{nextMove.toUpperCase()}'</h2>
</div>

<div class="hflex">
	<div class="vflex">
		<h4>Board</h4>
		<div class="grid-container">
			{#if winner?.line}
				<svg class="line-overlay">
					<line
						class="win-line"
						class:xwline={winner?.player === 'x'}
						class:owline={winner?.player === 'o'}
						{...winner.line}
						stroke="white"
						strokeWidth="4"
					/>
				</svg>
			{/if}

			<div class="grid">
				{#each states as _, i}
					<Slot
						bind:state={states[i]}
						nextState={nextMove}
						canMove={!winner}
						postMoveAction={postMoveJob}
					/>
				{/each}
			</div>
		</div>
	</div>

	<div class="vflex">
		<h4>Rewind Panel</h4>
		<div class="rewindgrid">
			{#if round !== 0}
				<button class="rewindButton" onclick={reset}> Reset </button>
			{/if}
			{#each cachedStates as history, i}
				<button class="rewindButton" onclick={() => rewind(i)}>
					To Move {i + 1}
				</button>
			{/each}
		</div>
	</div>
</div>

{#if winner?.player === 'draw'}
	<h3>It's a DRAW!!!</h3>
{:else if winner}
	<h3>'{winner.player.toUpperCase()}' is the winner!!!</h3>
{:else}
	<h3>___</h3>
{/if}

<style>
	.vflex {
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.grid-container {
		position: relative;
		display: inline-block;
	}

	.grid {
		display: grid;
		grid-template-columns: repeat(3, 80px);
		grid-template-rows: repeat(3, 80px);
		gap: 10px;
		justify-content: center;
	}

	.line-overlay {
		position: absolute;
		z-index: 1;
		top: 0;
		left: 0;
		width: 100%;
		height: 100%;
		pointer-events: none;
	}

	.win-line {
		stroke: red;
		stroke-width: 4px;
		stroke-linecap: round;
		stroke-linejoin: round;
		opacity: 0.5;
	}

	.xwline {
		stroke: #b100e7;
	}

	.owline {
		stroke: #eb5e00;
	}

	.hflex {
		display: flex;
		flex-direction: row;
		align-items: center;
		justify-content: center;
		gap: 40px;
	}

	.rewindgrid {
		display: grid;
		grid-template-columns: repeat(2, 100px);
		grid-template-rows: repeat(5, 40px);
		align-items: center;
		justify-content: center;
		gap: 8px;
		padding: 10px;
		background-color: rgba(71, 48, 48, 0.156);
		border-radius: 5%;
		border: 2px dotted #494949;
	}

	.rewindButton {
		width: 100%;
		height: 100%;
		font-size: 0.85em;
		background-color: #36424eb3;
	}
</style>
