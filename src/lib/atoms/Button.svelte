<script lang="ts">
  import type { Snippet } from 'svelte';
  import gsap from 'gsap';
  let { 
    variant = 'primary', 
    size = 'md', 
    href = '', 
    children,
    onclick
  }: {
    variant?: 'primary' | 'secondary' | 'outline';
    size?: 'sm' | 'md' | 'lg';
    href?: string;
    children?: Snippet;
    onclick?: () => void;
  } = $props();
  
  const baseClasses = 'inline-flex items-center justify-center font-semibold rounded-full transition-all duration-300 transform hover:scale-105 active:scale-95';
  
  const variants = {
    primary: 'bg-primary text-white shadow-lg shadow-primary/30 hover:shadow-primary/50',
    secondary: 'bg-accent text-white shadow-lg shadow-accent/30 hover:shadow-accent/50',
    outline: 'border-2 border-primary text-primary hover:bg-primary hover:text-white'
  };
  
  const sizes = {
    sm: 'px-4 py-2 text-sm',
    md: 'px-8 py-4 text-base',
    lg: 'px-12 py-5 text-lg'
  };

  let buttonEl: HTMLElement | HTMLAnchorElement;

  function handleMouseMove(e: MouseEvent) {
    const { clientX, clientY } = e;
    const { left, top, width, height } = buttonEl.getBoundingClientRect();
    const x = clientX - (left + width / 2);
    const y = clientY - (top + height / 2);
    
    gsap.to(buttonEl, {
      x: x * 0.3,
      y: y * 0.3,
      duration: 0.5,
      ease: "power2.out"
    });
  }

  function handleMouseLeave() {
    gsap.to(buttonEl, {
      x: 0,
      y: 0,
      duration: 0.8,
      ease: "elastic.out(1, 0.3)"
    });
  }
</script>

{#if href}
  <a {href} 
     bind:this={buttonEl}
     onmousemove={handleMouseMove}
     onmouseleave={handleMouseLeave}
     class="{baseClasses} {variants[variant]} {sizes[size]}" data-sveltekit-reload>
    {@render children?.()}
  </a>
{:else}
  <button 
     bind:this={buttonEl}
     onmousemove={handleMouseMove}
     onmouseleave={handleMouseLeave}
     class="{baseClasses} {variants[variant]} {sizes[size]}" {onclick}>
    {@render children?.()}
  </button>
{/if}
