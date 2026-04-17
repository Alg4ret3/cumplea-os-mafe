<script lang="ts">
	import HeroSection from '../organisms/HeroSection.svelte';
	import HorizontalSection from '../organisms/HorizontalSection.svelte';
	import AudioMessageSection from '../organisms/AudioMessageSection.svelte';
	import Typography from '../atoms/Typography.svelte';
</script>

<main class="relative w-full overflow-x-hidden">
	<HeroSection />
	<HorizontalSection />
	<AudioMessageSection />

	<!-- Footer -->
	<footer class="border-t border-gray-100 bg-white py-8">
		<Typography tag="p" variant="caption" color="gray" align="center">
			Abril 2026 • Con amor, Sergio Muñoz
		</Typography>
	</footer>
</main>
