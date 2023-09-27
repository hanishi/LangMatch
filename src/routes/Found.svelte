<script lang="ts">
    import { afterUpdate } from 'svelte';
    import {flip} from 'svelte/animate';
    import {receive} from './transitions';
    import {fade} from 'svelte/transition';
	import { curry_get_offset } from './utils';
    export let found: number[];
    export let height: number;
    export let width: number;
    export let squareSize: number;
    $: dimension = squareSize * 0.5;
    $: backgroundSize = `${width * dimension / height}px ${height * dimension / height}px`;
    $: get_offset = curry_get_offset(dimension);
</script>

<div class="found">
    {#each found as index (index)}
        <div in:fade={{delay: 500}} animate:flip={{duration: 200, delay: 500}} class="pair">
            <div class="sprite-element" 
            style:width={`${dimension}px`}
            style:height={`${dimension}px`}
            style:background-position={get_offset(index)} 
            style:background-size={backgroundSize} in:receive={{key: `${index}:a`}}/>
            <div class="sprite-element" 
            style:width={`${dimension}px`}
            style:height={`${dimension}px`} 
            style:background-position={get_offset(index)} 
            style:background-size={backgroundSize} in:receive={{key: `${index}:b`}}/>
        </div>
    {/each} 
</div>

<style>
    .found {
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        align-items: center;
        z-index: 3;
        height: 100%;
        width: 100%;
        filter: drop-shadow(0.2em 0.4em 0.6em rgba(0, 0, 0, 0.1));
    }
    .pair {
        display: flex;
        align-items: center;
        height: 100%;
        justify-content: center;
        aspect-ratio: 1;
        border-radius: 50%;
    }

	.sprite-element {
		position: absolute;
		z-index: 2;
		background: url('/spritesheet.png');
		border: 3px;
		border-radius: 6px;
		pointer-events: none;
	}
</style>
