<script lang="ts">
  import { onMount } from 'svelte';

  let { src = "https://res.cloudinary.com/dqky6oqrd/video/upload/so_10/v1775931646/c6m08bobq30y4j18yzuz.mp3" } = $props();
  
  let audio: HTMLAudioElement;
  let isPlaying = $state(false);
  let hasInteracted = $state(false);

  function toggleMusic() {
    if (isPlaying) {
      audio.pause();
    } else {
      audio.play().catch(e => console.log("Autoplay blocked", e));
    }
    isPlaying = !isPlaying;
    hasInteracted = true;
  }

  onMount(() => {
    const handleFirstInteraction = () => {
      if (!hasInteracted) {
        audio.play()
             .then(() => { if (!isPlaying) isPlaying = true; })
             .catch(() => {});
        window.removeEventListener('scroll', handleFirstInteraction);
      }
    };
    window.addEventListener('scroll', handleFirstInteraction);
    return () => window.removeEventListener('scroll', handleFirstInteraction);
  });
</script>

<div class="fixed bottom-10 left-10 z-[100] w-14 h-32 flex items-end justify-center pointer-events-none">
  <!-- Jumping Call to Action (NOW ABSOLUTE TO NOT PUSH BUTTON) -->
  {#if !isPlaying}
    <div class="absolute bottom-16 animate-bounce-custom whitespace-nowrap pointer-events-auto">
        <div class="bg-dark text-white text-[10px] font-numbers font-bold px-4 py-2 rounded-full shadow-2xl relative tracking-[1px] uppercase">
            ¡Escúchame!
            <!-- Little speech bubble arrow -->
            <div class="absolute -bottom-1 left-1/2 -translate-x-1/2 w-2.5 h-2.5 bg-dark rotate-45"></div>
        </div>
    </div>
  {/if}

  <button 
    onclick={toggleMusic}
    class="pointer-events-auto relative flex items-center justify-center w-14 h-14 bg-white rounded-full shadow-2xl border border-black/5 hover:scale-105 active:scale-95 transition-all duration-300"
    aria-label="Reproducir Música"
  >
    {#if isPlaying}
      <div class="absolute inset-0 rounded-full bg-black/5 animate-ping opacity-20"></div>
    {/if}

    <div class="text-black transition-opacity duration-300 {isPlaying ? 'opacity-100' : 'opacity-30'}">
      {#if isPlaying}
        <svg xmlns="http://www.w3.org/2000/svg" width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round">
          <path d="M11 5L6 9H2v6h4l5 4V5z"></path>
          <path d="M19.07 4.93a10 10 0 0 1 0 14.14"></path>
          <path d="M15.54 8.46a5 5 0 0 1 0 7.07"></path>
        </svg>
      {:else}
        <svg xmlns="http://www.w3.org/2000/svg" width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round">
          <path d="M11 5L6 9H2v6h4l5 4V5z"></path>
          <line x1="23" y1="9" x2="17" y2="15"></line>
          <line x1="17" y1="9" x2="23" y2="15"></line>
        </svg>
      {/if}
    </div>
  </button>

  <audio bind:this={audio} loop {src} preload="auto"></audio>
</div>

<style>
  @keyframes bounce-custom {
    0%, 100% {
      transform: translateY(0) scale(1);
      animation-timing-function: cubic-bezier(0.8, 0, 1, 1);
    }
    50% {
      transform: translateY(-12px) scale(1.02);
      animation-timing-function: cubic-bezier(0, 0, 0.2, 1);
    }
  }
  .animate-bounce-custom {
    animation: bounce-custom 1s infinite;
  }
  
  @keyframes ping {
    75%, 100% {
      transform: scale(2);
      opacity: 0;
    }
  }
  .animate-ping {
    animation: ping 2s cubic-bezier(0, 0, 0.2, 1) infinite;
  }
</style>
