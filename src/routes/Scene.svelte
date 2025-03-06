<script lang="ts">
	import { T } from '@threlte/core';
	import { OrbitControls } from '@threlte/extras';
	import { RigidBody } from '@threlte/rapier';

	// Coin spawn settings
	let spawnHeight = 10;
	let coins = $state<(typeof RigidBody)[]>([]);
	let coinCount = $state(0);

	// Random position within bounds
	function getRandomPosition() {
		return {
			x: (Math.random() - 0.5) * 8,
			y: spawnHeight,
			z: (Math.random() - 0.5) * 8
		};
	}

	// Random rotation
	function getRandomRotation() {
		return {
			x: Math.random() * Math.PI * 2,
			y: Math.random() * Math.PI * 2,
			z: Math.random() * Math.PI * 2
		};
	}

	// Function to spawn a coin
	function spawnCoin() {
		// Limit total coins for performance
		if (coins.length >= 20) {
			// Remove oldest coin
			const oldestCoin = coins[0];
			// if (oldestCoin) {
			// 	oldestCoin.setTranslation(new Vector3(100, 100, 100));
			// 	oldestCoin.sleep();
			// }
			// coins = coins.slice(1);
		}

		// Create new coin
		coinCount++;
	}

	// Initial coin and interval setup
	$effect(() => {
		// Create first coin immediately
		spawnCoin();
		// Then set up interval for more coins
		const interval = setInterval(spawnCoin, 1000);
		return () => clearInterval(interval);
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
<RigidBody type="fixed">
	<T.Mesh rotation.x={-Math.PI / 2} receiveShadow>
		<T.BoxGeometry args={[10, 10, 0.5]} />
		<T.MeshStandardMaterial color="#f0f0f0" />
	</T.Mesh>
</RigidBody>

<RigidBody type="fixed">
	<T.Mesh position={[0, 2, -5]} receiveShadow>
		<T.BoxGeometry args={[10, 4, 0.2]} />
		<T.MeshStandardMaterial color="#e0e0e0" transparent opacity={0.5} />
	</T.Mesh>

	<T.Mesh position={[0, 2, 5]} receiveShadow>
		<T.BoxGeometry args={[10, 4, 0.2]} />
		<T.MeshStandardMaterial color="#e0e0e0" transparent opacity={0.5} />
	</T.Mesh>

	<T.Mesh position={[-5, 2, 0]} rotation.y={Math.PI / 2} receiveShadow>
		<T.BoxGeometry args={[10, 4, 0.2]} />
		<T.MeshStandardMaterial color="#e0e0e0" transparent opacity={0.5} />
	</T.Mesh>

	<T.Mesh position={[5, 2, 0]} rotation.y={Math.PI / 2} receiveShadow>
		<T.BoxGeometry args={[10, 4, 0.2]} />
		<T.MeshStandardMaterial color="#e0e0e0" transparent opacity={0.5} />
	</T.Mesh>
</RigidBody>

<!-- Dynamic Coins -->
{#each Array(coinCount) as _, i}
	{@const pos = getRandomPosition()}
	{@const rot = getRandomRotation()}
	<RigidBody
		gravityScale={1}
		linearDamping={0.2}
		angularDamping={0.2}
		oncreate={(body) => {
			coins = [...coins, body];
			return () => {
				coins = coins.filter((c) => c !== body);
			};
		}}
	>
		<T.Mesh castShadow>
			<T.CylinderGeometry args={[0.5, 0.5, 0.1, 32]} />
			<T.MeshStandardMaterial color="#ffd700" metalness={0.8} roughness={0.2} />
		</T.Mesh>
	</RigidBody>
{/each}
