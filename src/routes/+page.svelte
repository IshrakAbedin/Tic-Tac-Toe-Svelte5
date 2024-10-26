<script>
	import '$lib/common.css';
	import Game from '$lib/TicTacToe/Game.svelte';
</script>

<div>
	<h1 class="title">Tic-Tac-Toe</h1>
	<Game />
</div>

<style>
	div {
		display: flex;
		flex-direction: column;
		align-items: center;
	}

	.title {
		font-family: 'Cookie', 'Shantell Sans', cursive;
		background: linear-gradient(to top right, #e2aa4a, #a600ff); /* Blue gradient */
		-webkit-background-clip: text; /* Clip background to text */
		-webkit-text-fill-color: transparent; /* Make text fill transparent */
		background-clip: text;
		color: transparent;
	}
</style>
