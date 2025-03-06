<script lang="ts">
	import { T } from '@threlte/core';
	import { OrbitControls, AudioListener } from '@threlte/extras';
	import Emitter from './Emitter.svelte';
	import Ground from './Ground.svelte';
</script>

<T.PerspectiveCamera makeDefault position={[12, 8, 12]} fov={45}>
	<OrbitControls 
		enableZoom
		minDistance={10}
		maxDistance={20}
		target={[0, 0, 0]} 
		maxPolarAngle={Math.PI * 0.6}
		minPolarAngle={Math.PI * 0.1}
		enableDamping
		dampingFactor={0.05}
	/>
	<AudioListener />
</T.PerspectiveCamera>

<!-- Main Light -->
<T.DirectionalLight castShadow position={[10, 15, 5]} intensity={1.2}>
	<T.OrthographicCamera attach="shadow-camera" args={[-10, 10, 10, -10, 0.1, 50]} />
</T.DirectionalLight>

<!-- Fill Light -->
<T.DirectionalLight position={[-5, 10, -5]} intensity={0.4} />

<!-- Ambient Light -->
<T.AmbientLight intensity={0.4} />

<Ground />

<Emitter />
