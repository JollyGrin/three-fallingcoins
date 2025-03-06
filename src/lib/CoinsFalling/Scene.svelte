<script lang="ts">
	import { T } from '@threlte/core';
	import { OrbitControls, AudioListener } from '@threlte/extras';
	import Emitter from './Emitter.svelte';
	import Ground from './Ground.svelte';
</script>

<T.PerspectiveCamera makeDefault position={[0, 2, 12]} fov={45}>
	<OrbitControls
		autoRotate
		rotateSpeed={0.1}
		enableZoom
		minDistance={10}
		maxDistance={25}
		target={[0, 0, 0]}
		maxPolarAngle={Math.PI * 0.5}
		minPolarAngle={Math.PI * 0.1}
		enableDamping
		dampingFactor={0.05}
	/>
	<AudioListener />
</T.PerspectiveCamera>

<!-- Main Light -->
<T.DirectionalLight castShadow position={[10, 15, 5]} intensity={3}>
	<T.OrthographicCamera attach="shadow-camera" args={[-10, 10, 10, -10, 0.1, 50]} />
</T.DirectionalLight>

<!-- Fill Light -->
<T.DirectionalLight position={[-5, 10, -5]} intensity={0.4} />

<!-- Ambient Light -->
<T.AmbientLight intensity={1} />

<Ground />

<Emitter />
