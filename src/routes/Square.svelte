<script lang="ts">
	import { send } from './transitions';
	export let value: number;
	export let selected: boolean;
	export let found: boolean;
	export let group: 'a' | 'b';
	export let offset: string;
	export let backgroundSize: string;
</script>

<div class="square" class:flipped={selected || found}>
	<button on:click disabled={selected || found} />
	{#if !found}
		<div
			class="sprite-element"
			style:background-position={offset}
			style:background-size={backgroundSize}
			out:send={{ key: `${value}:${group}` }}
		/>
	{/if}
</div>

<style>
	.square {
		display: flex;
		width: 100%;
		height: 100%;
		align-items: center;
		justify-content: center;
		transition: filter 0.2s;
		transform-style: preserve-3d;
		transform: rotateY(180deg);
		transition: transform 0.4s;
		user-select: none;
	}

	.square * {
		width: 100%;
		height: 100%;
		backface-visibility: hidden;
	}

	button {
		position: absolute;
		width: 100%;
		height: 100%;
		border: none;
		display: flex;
		justify-content: center;
		align-items: center;
		background: var(--bg-2);
		border-radius: 1em;
		transform: rotateY(180deg);
		-webkit-tap-highlight-color: transparent;
	}

	button:disabled {
		color: inherit;
	}

	.flipped {
		transform: rotateY(0);
		z-index: 2;
	}

	.sprite-element {
		position: absolute;
		z-index: 2;
		width: 100%;
		height: 100%;
		background: url('/spritesheet.png');
		border: 3px;
		border-radius: 6px;
		pointer-events: none;
		background-repeat: no-repeat;
	}
</style>
