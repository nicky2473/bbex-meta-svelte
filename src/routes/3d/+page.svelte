<script>
	import { Canvas } from '@threlte/core';
	import { World } from '@threlte/rapier';
	import Scene from './Scene.svelte';
</script>

<div class="absolute left-1/2 -translate-x-1/2 top-5 p-5 rounded-2xl bg-gray-300 bg-opacity-25">
	WASD를 통해 이동, 마우스 클릭해 주변 살피기
</div>
<div class="w-screen h-screen">
	<Canvas useLegacyLights={true}>
		<World>
			<Scene />
		</World>
	</Canvas>
</div>
