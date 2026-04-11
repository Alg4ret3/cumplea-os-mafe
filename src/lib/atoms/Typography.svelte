<script lang="ts">
  import type { Snippet } from 'svelte';

  let {
    tag = 'p',
    variant = 'body',
    align = 'left',
    color = 'dark',
    children
  }: {
    tag?: 'h1' | 'h2' | 'h3' | 'p' | 'span';
    variant?: 'display' | 'title' | 'subtitle' | 'body' | 'caption';
    align?: 'left' | 'center' | 'right';
    color?: 'white' | 'primary' | 'dark' | 'gray';
    children: Snippet;
  } = $props();
  
  const alignments = {
    left: 'text-left',
    center: 'text-center',
    right: 'text-right'
  };

  const colors = {
    white: 'text-white',
    primary: 'text-primary',
    dark: 'text-dark',
    gray: 'text-gray-500'
  };

  const variants = {
    display: 'text-6xl md:text-9xl font-bold font-display italic tracking-tighter',
    title: 'text-5xl md:text-7xl font-bold font-display',
    subtitle: 'text-xl md:text-2xl font-light tracking-[0.2em] uppercase',
    body: 'text-base md:text-xl font-light leading-relaxed',
    caption: 'text-xs md:text-sm font-medium uppercase tracking-[0.3em]'
  };
</script>

<svelte:element this={tag} class="{variants[variant]} {alignments[align]} {colors[color]}">
  {@render children()}
</svelte:element>
