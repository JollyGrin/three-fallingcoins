<script lang="ts">
	import { T } from '@threlte/core';
	import { OrbitControls } from '@threlte/extras';
	import { RigidBody } from '@threlte/rapier';
	import { MathUtils } from 'three';

	// Coin spawn settings
	let spawnHeight = 10;
	let coins = $state([]);
	let coinCount = $state(0);

	// Function to spawn a coin
	function spawnCoin() {
		// Limit total coins for performance
		if (coins.length >= 20) {
			// Remove oldest coin
			const oldestCoin = coins[0];
			oldestCoin?.sleep();
			coins = coins.slice(1);
		}

		// Create new coin with random position and rotation
		coinCount++;
		const x = MathUtils.randFloat(-3, 3);
		const z = MathUtils.randFloat(-3, 3);
		const rotX = MathUtils.randFloat(0, Math.PI);
		const rotY = MathUtils.randFloat(0, Math.PI);
		const rotZ = MathUtils.randFloat(0, Math.PI);

		// Add coin to scene
		coins = [...coins, null]; // Placeholder until onCreate callback
	}

	// Spawn a coin every second
	let spawnInterval: number;
	$effect(() => {
		spawnInterval = setInterval(spawnCoin, 1000);
		return () => clearInterval(spawnInterval);
	});
</script>

<!-- Camera -->
<T.PerspectiveCamera
	makeDefault
	position={[10, 15, 10]}
	fov={50}
	oncreate={(ref) => {
		ref.lookAt(0, 0, 0);
	}}
>
	<OrbitControls />
</T.PerspectiveCamera>

<!-- Lights -->
<T.DirectionalLight position={[3, 10, 10]} intensity={1.5} castShadow />
<T.AmbientLight intensity={0.5} />

<!-- Ground plane -->
<RigidBody type="fixed" friction={0.8}>
	<T.Mesh rotation.x={-Math.PI / 2} receiveShadow>
		<T.PlaneGeometry args={[10, 10]} />
		<T.MeshStandardMaterial color="#f0f0f0" />
	</T.Mesh>
</RigidBody>

<!-- Boundary Walls -->
<RigidBody type="fixed" friction={0.2}>
	<!-- Back wall -->
	<T.Mesh position={[0, 2, -5]} receiveShadow>
		<T.BoxGeometry args={[10, 4, 0.2]} />
		<T.MeshStandardMaterial color="#e0e0e0" transparent opacity={0.5} />
	</T.Mesh>
	<!-- Front wall -->
	<T.Mesh position={[0, 2, 5]} receiveShadow>
		<T.BoxGeometry args={[10, 4, 0.2]} />
		<T.MeshStandardMaterial color="#e0e0e0" transparent opacity={0.5} />
	</T.Mesh>
	<!-- Left wall -->
	<T.Mesh position={[-5, 2, 0]} rotation.y={Math.PI / 2} receiveShadow>
		<T.BoxGeometry args={[10, 4, 0.2]} />
		<T.MeshStandardMaterial color="#e0e0e0" transparent opacity={0.5} />
	</T.Mesh>
	<!-- Right wall -->
	<T.Mesh position={[5, 2, 0]} rotation.y={Math.PI / 2} receiveShadow>
		<T.BoxGeometry args={[10, 4, 0.2]} />
		<T.MeshStandardMaterial color="#e0e0e0" transparent opacity={0.5} />
	</T.Mesh>
</RigidBody>

<!-- Dynamic Coins -->
{#each Array(coinCount) as _, i}
	<RigidBody
		position={[MathUtils.randFloat(-3, 3), spawnHeight, MathUtils.randFloat(-3, 3)]}
		rotation={[
			MathUtils.randFloat(0, Math.PI),
			MathUtils.randFloat(0, Math.PI),
			MathUtils.randFloat(0, Math.PI)
		]}
		restitution={0.3}
		friction={0.5}
		onCreate={(body) => {
			const index = coins.indexOf(null);
			if (index !== -1) {
				coins[index] = body;
			}
		}}
		onDestroy={(body) => {
			coins = coins.filter((c) => c !== body);
		}}
	>
		<T.Mesh castShadow>
			<T.CylinderGeometry args={[0.5, 0.5, 0.1, 32]} />
			<T.MeshStandardMaterial color="#ffd700" metalness={0.8} roughness={0.2} />
		</T.Mesh>
	</RigidBody>
{/each}
