<script lang="ts">
  import { onMount } from 'svelte';
  import gsap from 'gsap';
  import Typography from '../atoms/Typography.svelte';

  let sectionRef: HTMLElement;

  onMount(() => {
    gsap.from('.message-text', {
      scrollTrigger: {
        trigger: sectionRef,
        start: 'top 80%',
        end: 'bottom 20%',
        toggleActions: 'play reverse play reverse'
      },
      y: 50,
      opacity: 0,
      duration: 1,
      stagger: 0.3
    });
  });
</script>

<section 
  bind:this={sectionRef}
  class="min-h-screen w-full flex flex-col items-center justify-center bg-white relative px-6"
>
  <div class="message-text">
    <Typography tag="h2" variant="subtitle" color="dark" align="center">
      Un capítulo nuevo
    </Typography>
  </div>
  
  <div class="message-text mt-8 max-w-xl">
    <Typography tag="p" variant="title" align="center" color="dark">
      "Eres la prueba de que las mejores cosas llegan cuando menos las esperas."
    </Typography>
  </div>

  <div class="message-text mt-12 border-l-2 border-black/10 pl-6 opacity-50">
    <Typography tag="p" variant="body" color="dark" align="center">
      Gracias por ser tú y por cada momento compartido.
    </Typography>
  </div>
</section>
