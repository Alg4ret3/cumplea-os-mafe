<script lang="ts">
  import { onMount } from 'svelte';
  import gsap from 'gsap';
  import type { Snippet } from 'svelte';

  let { 
    text = "", 
    children, 
    delay = 0 
  }: { 
    text?: string; 
    children?: Snippet; 
    delay?: number 
  } = $props();

  let element: HTMLElement;

  onMount(() => {
    const target = element.querySelector('.reveal-content') || element;
    
    gsap.fromTo(target, 
      { 
        y: "100%", 
        rotate: 5,
        opacity: 0 
      },
      { 
        y: "0%", 
        rotate: 0,
        opacity: 1,
        duration: 1.5, 
        delay, 
        ease: "expo.out" 
      }
    );
  });
</script>

<div bind:this={element} class="overflow-hidden inline-block">
  <div class="reveal-content inline-block">
    {#if children}
      {@render children()}
    {:else}
      {text}
    {/if}
  </div>
</div>
