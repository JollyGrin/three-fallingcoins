<script lang="ts">
	import { Canvas } from '@threlte/core';
	import { HTML } from '@threlte/extras';
	import { World } from '@threlte/rapier';
	import { muted } from './Particle.svelte';
	import Scene from './Scene.svelte';
</script>

<div title="Rigid Body" class="fixed">
	<button
		title="toggle sound"
		class="h-6 w-fit bg-red-500 text-xs"
		on:click={() => {
			$muted = !$muted;
		}}>audio is muted: {$muted}</button
	>
</div>

<div>
	<Canvas>
		<World>
			<Scene />

			{#snippet fallback()}
				<HTML transform>
					<p>
						It seems your browser<br />
						doesn't support WASM.<br />
						I'm sorry.
					</p>
				</HTML>
			{/snippet}
		</World>
	</Canvas>
</div>

<style>
	div {
		height: 100%;
	}
	p {
		font-size: 0.75rem;
		line-height: 1rem;
	}
</style>
