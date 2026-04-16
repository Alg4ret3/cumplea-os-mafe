<script lang="ts">
	import HeroSection from '../organisms/HeroSection.svelte';
	import HorizontalSection from '../organisms/HorizontalSection.svelte';
	import Typography from '../atoms/Typography.svelte';
</script>

<main class="relative w-full overflow-x-hidden">
	<HeroSection />
	<HorizontalSection />

	<!-- Footer -->
	<footer class="border-t border-gray-100 bg-white py-8">
		<Typography tag="p" variant="caption" color="dark" align="center">
			Hecho con Amor para Mafe • 2024
		</Typography>
	</footer>
</main>
