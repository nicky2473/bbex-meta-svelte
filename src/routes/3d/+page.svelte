<script>
	import { Canvas } from '@threlte/core';
	import { World } from '@threlte/rapier';
	import Scene from './Scene.svelte';
</script>

<div class="w-screen h-screen">
	<Canvas useLegacyLights={true}>
		<World>
			<Scene />
		</World>
	</Canvas>
</div>
