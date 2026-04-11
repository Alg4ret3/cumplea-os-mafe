<script lang="ts">
  import { onMount } from 'svelte';
  import gsap from 'gsap';
  import { ScrollTrigger } from 'gsap/dist/ScrollTrigger';
  import Lenis from 'lenis';
  import MusicPlayer from '$lib/molecules/MusicPlayer.svelte';
  import './layout.css';

  let { children } = $props();

  onMount(() => {
    // 1. Initialize Lenis
    const lenis = new Lenis({
      duration: 1.2,
      easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)),
      orientation: 'vertical',
      gestureOrientation: 'vertical',
      smoothWheel: true,
      wheelMultiplier: 1,
      infinite: false,
    });

    // 2. Sync Lenis with ScrollTrigger
    lenis.on('scroll', ScrollTrigger.update);

    gsap.ticker.add((time) => {
      lenis.raf(time * 1000);
    });

    gsap.ticker.lagSmoothing(0);

    return () => {
      lenis.destroy();
    };
  });
</script>

<div class="lenis-content">
  <MusicPlayer />
  {@render children()}
</div>

<style>
  :global(html.lenis, html.lenis body) {
    height: auto;
  }

  .lenis-content {
    width: 100%;
  }
</style>
