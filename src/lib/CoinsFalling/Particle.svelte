<script lang="ts" module>
	const geometry = new CylinderGeometry(0.5, 0.5, 0.1, 32);
	const material = new MeshStandardMaterial({
		color: '#ffd700',
		metalness: 0.9,
		roughness: 0.3
	});
	export const muted = writable(true);
</script>

<script lang="ts">
	import { T } from '@threlte/core';
	import { PositionalAudio } from '@threlte/extras';
	import { Collider, RigidBody, type ContactEvent } from '@threlte/rapier';
	import { writable } from 'svelte/store';
	import type { Euler, Vector3 } from 'three';
	import { CylinderGeometry, MeshStandardMaterial } from 'three';
	import { clamp } from 'three/src/math/MathUtils.js';

	let {
		position,
		rotation
	}: {
		position?: Vector3;
		rotation?: Euler;
	} = $props();

	const audios: {
		threshold: number;
		volume: number;
		stop: (() => any) | undefined;
		play: ((...args: any[]) => any) | undefined;
		source: string;
	}[] = new Array(9).fill(0).map((_, i) => {
		return {
			threshold: i / 10,
			play: undefined,
			stop: undefined,
			volume: (i + 2) / 10,
			source: `/audio/ball_bounce_${i + 1}.mp3`
		};
	});

	const fireSound: ContactEvent = (event) => {
		if ($muted) return;
		const volume = clamp((event.totalForceMagnitude - 30) / 1100, 0.1, 1);
		const audio = audios.find((a) => a.volume >= volume);
		audio?.stop?.();
		audio?.play?.();
	};

	let rotationCasted: [x: number, y: number, z: number] = $state([0, 0, 0]);
	$effect(() => {
		rotationCasted = rotation?.toArray() as [x: number, y: number, z: number];
	});
</script>

<T.Group
	position.x={position?.x}
	position.y={position?.y}
	position.z={position?.z}
	rotation={rotationCasted}
>
	<RigidBody type={'dynamic'} oncontact={fireSound}>
		{#each audios as audio}
			<PositionalAudio
				autoplay={false}
				detune={600 - Math.random() * 1200}
				stop={audio.stop}
				play={audio.play}
				src={audio.source}
				volume={audio.volume}
			/>
		{/each}

		<Collider
			contactForceEventThreshold={30}
			restitution={0.3}
			friction={0.5}
			shape={'cylinder'}
			args={[0.05, 0.5]}
		/>
		<T.Mesh castShadow receiveShadow {geometry} {material} />
	</RigidBody>
</T.Group>
