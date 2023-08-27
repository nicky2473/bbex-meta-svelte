<script lang="ts">
	import type { RigidBody as RapierRigidBody } from '@dimforge/rapier3d-compat';
	import { T, useFrame, useThrelte } from '@threlte/core';
	import { RigidBody, CollisionGroups, Collider } from '@threlte/rapier';
	import { PerspectiveCamera, Vector3 } from 'three';
	import PointerControls from './PointerControls.svelte';

	export let position: [x: number, y: number, z: number] = [0, 0, 0];
	let radius = 0.3;
	let height = 1.7;
	export let speed = 3;

	let rigidBody: RapierRigidBody;
	let cam: PerspectiveCamera;

	let forward = 0;
	let backward = 0;
	let left = 0;
	let right = 0;
	let panning = false;

	const t = new Vector3();

	const { renderer } = useThrelte();

	useFrame(() => {
		if (!rigidBody) return;
		// get direction
		const velVec = t.fromArray([right - left, 0, backward - forward]);
		// sort rotate and multiply by speed
		velVec.applyEuler(cam.rotation).multiplyScalar(speed);
		// don't override falling velocity
		const linVel = rigidBody.linvel();
		t.y = linVel.y;
		// finally set the velocities and wake up the body
		rigidBody.setLinvel(t, true);

		// when body position changes update position prop for camera
		const pos = rigidBody.translation();
		position = [pos.x, pos.y, pos.z];
	});

	function onKeyDown(e: KeyboardEvent) {
		switch (e.code) {
			case 'Space':
				panning = true;
				break;
			case 'KeyS':
				backward = 1;
				break;
			case 'KeyW':
				forward = 1;
				break;
			case 'KeyA':
				left = 1;
				break;
			case 'KeyD':
				right = 1;
				break;
			default:
				break;
		}
	}

	function onKeyUp(e: KeyboardEvent) {
		switch (e.code) {
			case 'Space':
				panning = false;
				renderer.domElement.style.cursor = 'default';
				break;
			case 'KeyS':
				backward = 0;
				break;
			case 'KeyW':
				forward = 0;
				break;
			case 'KeyA':
				left = 0;
				break;
			case 'KeyD':
				right = 0;
				break;
			default:
				break;
		}
	}
</script>

<svelte:window on:keydown|preventDefault={onKeyDown} on:keyup={onKeyUp} />

<T.Group position.y={0.9}>
	<T.PerspectiveCamera
		makeDefault
		fov={90}
		bind:ref={cam}
		position.x={position[0]}
		position.y={position[1]}
		position.z={position[2]}
		on:create={({ ref }) => {
			ref.lookAt(new Vector3(0, 2, 0));
		}}
	>
		<PointerControls {panning} />
	</T.PerspectiveCamera>
</T.Group>

<T.Group {position}>
	<RigidBody bind:rigidBody {position} enabledRotations={[false, false, false]}>
		<CollisionGroups groups={[0]}>
			<Collider shape={'capsule'} args={[height / 2 - radius, radius]} />
		</CollisionGroups>

		<CollisionGroups groups={[15]}>
			<T.Group position={[0, -height / 2 + radius, 0]}>
				<Collider sensor shape={'ball'} args={[radius * 1.2]} />
			</T.Group>
		</CollisionGroups>
	</RigidBody>
</T.Group>
