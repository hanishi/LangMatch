<script lang="ts">
	import Game from './Game.svelte';
	import '../styles.css';
	import Modal from './Modal.svelte';
	let state: 'waiting' | 'playing' | 'paused' | 'won' | 'lost' = 'waiting';

	let game: Game;
</script>

<svelte:head>
	<title>lang-match</title>
</svelte:head>

<main>
	<Game
		bind:this={game}
		on:play={() => {
			state = 'playing';
		}}
		on:pause={() => {
			state = 'paused';
		}}
		on:win={() => {
			state = 'won';
		}}
		on:lose={() => {
			state = 'lost';
		}}
	/>
	{#if state !== 'playing'}
		<Modal>
			<header>
				<h3>langmatchi</h3>
				<p>forked and modified the https://github.com/Rich-Harris/ematchi</p>
			</header>
			{#if state === 'won' || state === 'lost'}
				<p>You {state}!</p>
			{:else if state === 'paused'}
				<p>game paused</p>
			{:else if state === 'waiting'}
				<p>Flip to find your favorite programming language!</p>
			{/if}

			<div class="buttons">
				{#if state === 'paused'}
					<button on:click={() => game.resume()}>resume</button>
					<button on:click={() => (state = 'waiting')}>end</button>
				{:else}
					<button
						on:click={() => {
							game.start();
							state = 'playing';
						}}
					>
					start new game	
					</button>
				{/if}
			</div>
		</Modal>
	{/if}
</main>

<style>
	main {
		text-align: center;
		height: 100%;
		display: flex;
		flex-direction: column;
		justify-content: center;
	}

	header {
		font-size: min(5vw, 2rem);
		font-family: Grandstander, sans-serif;
	}

	p {
		margin: 0 0 1em 0;
	}

	.buttons {
		display: flex;
		justify-content: center;
		gap: 0.5em;
	}

	button {
		background: var(--bg-3);
		color: white;
		font-size: inherit;
		font-family: inherit;
		border: none;
		padding: 1em;
		border-radius: 0.5em;
	}
</style>
