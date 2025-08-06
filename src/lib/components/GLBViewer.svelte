<script lang="ts">
	import { onMount } from 'svelte';
	import * as THREE from 'three';
	import { GLTFLoader } from 'three/examples/jsm/loaders/GLTFLoader.js';
	import { OrbitControls } from 'three/examples/jsm/controls/OrbitControls.js';

	let container: HTMLDivElement;

	onMount(() => {
		const scene = new THREE.Scene();
		scene.background = new THREE.Color(0xffffff); // background putih

		const camera = new THREE.PerspectiveCamera(75, container.clientWidth / container.clientHeight, 0.1, 1000);
    camera.position.set(0, 1, 1.5);

		const renderer = new THREE.WebGLRenderer({ antialias: true });
		renderer.setSize(container.clientWidth, container.clientHeight);
		container.appendChild(renderer.domElement);

		const ambientLight = new THREE.AmbientLight(0xffffff, 1);
		scene.add(ambientLight);

		const directionalLight = new THREE.DirectionalLight(0xffffff, 0.8);
		directionalLight.position.set(5, 10, 7.5);
		scene.add(directionalLight);

		// Orbit Controls
		const controls = new OrbitControls(camera, renderer.domElement);
		controls.enableDamping = true;
		controls.dampingFactor = 0.1;
		controls.enableZoom = true;
		controls.enablePan = true;

		const loader = new GLTFLoader();
		loader.load('/FastRescue.glb', (gltf) => {
			scene.add(gltf.scene);
		}, undefined, (err) => {
			console.error('Failed to load model:', err);
		});

		const handleResize = () => {
			camera.aspect = container.clientWidth / container.clientHeight;
			camera.updateProjectionMatrix();
			renderer.setSize(container.clientWidth, container.clientHeight);
		};
		window.addEventListener('resize', handleResize);

		const animate = () => {
			requestAnimationFrame(animate);
			controls.update();
			renderer.render(scene, camera);
		};
		animate();

		return () => {
			window.removeEventListener('resize', handleResize);
			renderer.dispose();
		};
	});
</script>

<div bind:this={container} style="width: 100%; height: 500px;" />

