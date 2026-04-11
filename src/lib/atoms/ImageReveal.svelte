<script lang="ts">
  import { onMount } from 'svelte';
  import gsap from 'gsap';

  let { 
    src, 
    alt = "Birthday image",
    delay = 0 
  }: { 
    src: string; 
    alt?: string;
    delay?: number 
  } = $props();

  let container: HTMLElement;
  let image: HTMLImageElement;

  onMount(() => {
    gsap.fromTo(container, 
      { 
        clipPath: 'inset(100% 0 0 0)' 
      },
      { 
        clipPath: 'inset(0% 0 0 0)',
        duration: 1.8,
        delay,
        ease: "power4.inOut",
        scrollTrigger: {
          trigger: container,
          start: "top 90%",
        }
      }
    );

    gsap.fromTo(image, 
      { 
        scale: 1.3 
      },
      { 
        scale: 1,
        duration: 2,
        delay,
        ease: "power4.out",
        scrollTrigger: {
          trigger: container,
          start: "top 90%",
        }
      }
    );
  });
</script>

<div 
  bind:this={container} 
  class="relative overflow-hidden w-full h-full rounded-2xl bg-gray-100"
>
  <img 
    bind:this={image}
    {src} 
    {alt} 
    class="w-full h-full object-cover"
  />
</div>
