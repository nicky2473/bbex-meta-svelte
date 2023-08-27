<script lang="ts">
	import { T, useFrame } from '@threlte/core';
	import { GLTF, OrbitControls } from '@threlte/extras';
	import { CollisionGroups } from '@threlte/rapier';
	import { spring } from 'svelte/motion';
	import { Mesh, Vector3 } from 'three';
	import Player from './Player.svelte';
	import Ground from './Ground.svelte';

	let playerMesh: Mesh;
	let positionHasBeenSet = false;
	const smoothPlayerPosX = spring(0);
	const smoothPlayerPosZ = spring(0);
	const t3 = new Vector3();

	useFrame(() => {
		if (!playerMesh) return;
		playerMesh.getWorldPosition(t3);
		smoothPlayerPosX.set(t3.x, {
			hard: !positionHasBeenSet
		});
		smoothPlayerPosZ.set(t3.z, {
			hard: !positionHasBeenSet
		});
		if (!positionHasBeenSet) positionHasBeenSet = true;
	});
</script>

<T.AmbientLight />
<T.DirectionalLight />

<CollisionGroups groups={[0, 15]}>
	<Ground />
</CollisionGroups>

<CollisionGroups groups={[0]}>
	<Player bind:playerMesh position={[0, 2, 3]} />
</CollisionGroups>

<GLTF url="/roomA/scene.gltf" position={[0, 0.5, 0]} castShadow receiveShadow />
