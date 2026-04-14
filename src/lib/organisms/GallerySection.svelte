<script lang="ts">
	import { onMount } from 'svelte';
	import gsap from 'gsap';

	let sectionRef: HTMLElement;

	onMount(() => {
		// GSAP animations here
	});
</script>

<section bind:this={sectionRef} class="w-full">
	<!-- Content here -->
</section>
