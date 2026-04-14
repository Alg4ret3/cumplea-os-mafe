<script lang="ts">
	import HeroSection from '../organisms/HeroSection.svelte';
	import GallerySection from '../organisms/GallerySection.svelte';
	import Typography from '../atoms/Typography.svelte';
</script>

<main class="relative w-full overflow-x-hidden">
	<HeroSection />
	<GallerySection />

	<!-- Content Footer -->
	<footer class="flex flex-col items-center justify-center border-t border-gray-100 bg-white py-20">
		<Typography tag="p" variant="caption" color="primary" align="center">
			Hecho con Amor para Mafe • 2024
		</Typography>
	</footer>
</main>
