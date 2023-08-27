<script lang="ts">
	import { T, useFrame } from '@threlte/core';
	import { GLTF, OrbitControls } from '@threlte/extras';
	import { CollisionGroups } from '@threlte/rapier';
	import { spring } from 'svelte/motion';
	import { Mesh, Vector3 } from 'three';
	import Player from './Player.svelte';
	import Ground from './Ground.svelte';

	type ViewType = 'top' | 'player';

	let playerMesh: Mesh;
	let positionHasBeenSet = false;
	let viewType: ViewType = 'player';

	const smoothPlayerPosX = spring(0);
	const smoothPlayerPosZ = spring(0);
	const t3 = new Vector3();

	function onKeyDown(e: KeyboardEvent) {
		if (e.code === 'ShiftLeft') {
			viewType = viewType === 'top' ? 'player' : 'top';
		}
	}

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

<svelte:window on:keydown|preventDefault={onKeyDown} />

<T.AmbientLight />
<T.DirectionalLight />

<CollisionGroups groups={[0, 15]}>
	<Ground />
</CollisionGroups>

{#if viewType === 'top'}
	<T.PerspectiveCamera makeDefault position={[0, 15, 0]}>
		<OrbitControls enableDamping />
	</T.PerspectiveCamera>
{:else}
	<CollisionGroups groups={[0]}>
		<Player bind:playerMesh position={[0, 2, 3]} />
	</CollisionGroups>
{/if}

<GLTF url="/roomA/scene.gltf" position={[0, 0.5, 0]} castShadow receiveShadow />
