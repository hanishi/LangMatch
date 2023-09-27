<script lang="ts">
	import Countdown from './Countdown.svelte';
	import Found from './Found.svelte';
	import Grid from './Grid.svelte';
	import { shuffle } from './utils';
	import { createEventDispatcher, onMount } from 'svelte';

	const dispatch = createEventDispatcher();

	let size: number = 4;
	let grid: number[] = [];
	let found: number[] = [];
	let duration: number;
	let remaining: number;

	let playing: boolean;
	let width: number;
	let height: number;

	let indices: number[];
	let squareSize: number;

	export function start() {
		remaining = duration = 30 * 1000;
		const sliced = indices.slice();
		const pairs: number[] = [];
		for (let i = 0; i < (size * size) / 2; i += 1) {
			const index = Math.floor(Math.random() * sliced.length);
			const value = sliced[index];
			sliced.splice(index, 1);
			pairs.push(value);
		}
		grid = shuffle([...pairs, ...pairs]);
		found = [];
		resume();
	}

	export function resume() {
		playing = true;
		countdown();
		dispatch('play');
	}

	function countdown() {
		const start = Date.now();
		const remaining_at_start = remaining;

		function loop() {
			if (!playing) return;
			requestAnimationFrame(loop);
			remaining = remaining_at_start - (Date.now() - start);
			if (remaining <= 0) {
				playing = false;
				dispatch('lose');
			}
		}
		loop();
	}
	onMount(() => {
		let img = document.createElement('img');
		img.src = '/spritesheet.png';
		img.onload = () => {
			width = img.width;
			height = img.height;
			indices = [...Array(width/height).keys()];
			img.remove();
		};	
	});
</script>

<div class="game" style="--size: {size}">
	<div class="info">
		{#if playing}
			<Countdown
				{remaining}
				{duration}
				on:click={() => {
					playing = false;
					dispatch('pause');
				}}
			/>
		{/if}
	</div>
	<div class="grid">
		{#key grid}
			<Grid
				bind:squareSize
				{size}
				{width}
				{height}
				{grid}
				{found}
				on:found={(event) => {
					found = [...found, event.detail.emoji];
					if (found.length === (size * size) / 2) {
						playing = false;
						setTimeout(() => {
							playing = false;
							dispatch('win');
						}, 1000);
					}
				}}
			/>
		{/key}
	</div>
	<div class="info">
		<Found {squareSize} {width} {height} {found} />
	</div>
</div>

<style>
	.game {
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		font-size: min(1vmin, 0.5em);
		gap: 2em;
		height: 100%;
	}

	.grid {
		display: grid;
		grid-template-columns: repeat(var(--size), 1fr);
		grid-template-rows: repeat(var(--size), 1fr);
		grid-gap: 1em;
		width: 80em;
		aspect-ratio: 1;
		perspective: 100vw;
		z-index: 2;
	}

	.info {
		display: flex;
		flex-direction: row;
		justify-content: center;
		align-items: center;
		width: 80em;
		height: 10em;
	}
</style>
