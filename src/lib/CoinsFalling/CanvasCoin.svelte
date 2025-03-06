<script lang="ts">
	import { Canvas } from '@threlte/core';
	import { HTML } from '@threlte/extras';
	import { World } from '@threlte/rapier';
	import { muted } from './Particle.svelte';
	import Scene from './Scene.svelte';
</script>

<div title="Rigid Body" class="fixed top-2 left-2 z-50">
	<a href="/" class="rounded bg-amber-200 p-2 text-xs">git</a>
	<button
		title="toggle sound"
		class=" h-fit w-fit cursor-pointer rounded bg-gray-300 p-2 text-xs"
		onclick={() => {
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
