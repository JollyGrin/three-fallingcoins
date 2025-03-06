<script lang="ts">
	import { T } from '@threlte/core';
	import { OrbitControls, AudioListener } from '@threlte/extras';
	import Emitter from './Emitter.svelte';
	import Ground from './Ground.svelte';
</script>

<T.PerspectiveCamera makeDefault position={[10, 10, 10]}>
	<OrbitControls enableZoom={false} />
	<AudioListener />
</T.PerspectiveCamera>

<T.DirectionalLight castShadow position={[8, 20, -3]} />

<Ground />

<Emitter />
