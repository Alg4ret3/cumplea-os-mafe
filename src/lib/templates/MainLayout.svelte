<script lang="ts">
  import HeroSection from '../organisms/HeroSection.svelte';
  import GallerySection from '../organisms/GallerySection.svelte';
  import MessageSection from '../organisms/MessageSection.svelte';
  import Typography from '../atoms/Typography.svelte';
</script>

<main class="relative w-full overflow-x-hidden">
  <HeroSection />
  <GallerySection />
  <MessageSection />
  
  <!-- Content Footer -->
  <footer class="py-20 flex flex-col items-center justify-center bg-white border-t border-gray-100">
      <Typography tag="p" variant="caption" color="primary" align="center">
          Hecho con Amor para Mafe • 2024
      </Typography>
  </footer>
</main>
