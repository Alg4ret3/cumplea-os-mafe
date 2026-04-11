<script lang="ts">
  import Typography from '../atoms/Typography.svelte';
  import Button from '../atoms/Button.svelte';
  import TextReveal from '../atoms/TextReveal.svelte';
  
  let { 
    name = "Mafe", 
    date = "15.05.2024" 
  }: {
    name?: string;
    date?: string;
  } = $props();
</script>

<div class="flex flex-col items-center gap-12 text-center max-w-2xl px-6">
  <div class="space-y-6">
    <TextReveal delay={0.5}>
      <Typography tag="span" variant="caption" color="primary" align="center">
        Especialmente para ti
      </Typography>
    </TextReveal>
    
    <div class="relative">
      <TextReveal delay={0.8}>
        <Typography tag="h1" variant="display" align="center">
          {name}
        </Typography>
      </TextReveal>
      <div class="absolute -bottom-4 left-1/2 -translate-x-1/2 w-48 h-px bg-primary/30"></div>
    </div>
    
    <TextReveal delay={1.1}>
      <Typography tag="p" variant="subtitle" color="gray" align="center">
        Un año más a tu lado
      </Typography>
    </TextReveal>
  </div>

  <Typography tag="p" variant="body" align="center" color="dark">
    Quiero celebrar la vida de la persona más especial.<br>
    Te espero para compartir una noche que guardaremos siempre.<br>
    <span class="text-primary font-semibold mt-4 block tracking-widest">{date}</span>
  </Typography>

  <Button variant="primary" size="lg" href="#rsvp">
    Ver Detalles
  </Button>
</div>
