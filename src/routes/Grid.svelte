<script lang="ts">
	import { createEventDispatcher } from 'svelte';
	import Square from './Square.svelte';
	import {onMount, onDestroy} from 'svelte';
	import { curry_get_offset } from './utils';
	import { squareSizeWritable } from './stores';
	export let found: number[];
	export let grid: number[];
	
	const dispatch = createEventDispatcher();
	let a: number;
	let b: number;
	let reset_timeout: ReturnType<typeof setTimeout>;

	export let size: number;
	export let width: number;
	export let height: number;
	let squareSize: number;
	let scaleFactor: number;

	function handle_resize() {
		const gridElement = document.querySelector('.grid');
		if (gridElement) {
			squareSize = Math.floor(gridElement.clientWidth / size * 0.95);
			scaleFactor = squareSize / height;
			squareSizeWritable.set(squareSize)
		}
	}
	$: backgroundSize = `${width * scaleFactor}px ${height * scaleFactor}px`;
	$: get_offset = curry_get_offset(squareSize);
	onMount(handle_resize);
    
</script>
<svelte:window on:resize={handle_resize} />
{#each grid as square, i}
	<Square offset={get_offset(square)}
		backgroundSize={backgroundSize}
		on:click={() => {
			if (a > -1 && b > -1) {
				clearTimeout(reset_timeout);
				a = i;
				b = -1;
			} else if (a > -1) {
				b = i;

				if (grid[a] === grid[b]) {
					found = [...found, grid[a]];
					dispatch('found', { emoji: grid[a] });
				} else {
					reset_timeout = setTimeout(() => {
						a = b = -1;
					}, 1000);
				}
			} else {
				a = i;
			}
		}}
		value={square}
		selected={a === i || b === i}
		found={found.includes(square)}
		group={i === grid.indexOf(square) ? 'a' : 'b'}
	/>
{/each}
