<script lang="ts">
  import Typography from '../atoms/Typography.svelte';
  import ImageReveal from '../atoms/ImageReveal.svelte';
</script>

<section class="min-h-screen py-32 px-6 bg-bg flex flex-col items-center">
  <div class="max-w-6xl w-full grid grid-cols-1 md:grid-cols-2 gap-12 md:gap-24">
    <div class="flex flex-col justify-center space-y-8">
      <Typography tag="h2" variant="title">Momentos que guardo en el corazón</Typography>
      <Typography tag="p" variant="body" color="gray">
        Cada instante contigo es un regalo. En este día tan especial, quiero recordar lo afortunado que soy de tenerte.
      </Typography>
    </div>
    
    <div class="h-[600px]">
      <ImageReveal 
        src="https://images.unsplash.com/photo-1516483638261-f4dbaf036963?q=80&w=1972&auto=format&fit=crop" 
        alt="Romantic Moment"
      />
    </div>

    <div class="h-[500px] md:mt-[-100px]">
      <ImageReveal 
        src="https://images.unsplash.com/photo-1516483638261-f4dbaf036963?q=80&w=1972&auto=format&fit=crop" 
        alt="Laughter"
        delay={0.3}
      />
    </div>

    <div class="flex flex-col justify-end space-y-6 pb-12">
      <Typography tag="p" variant="subtitle" color="primary">Gracias por ser tú</Typography>
      <div class="w-full h-px bg-primary/20"></div>
    </div>
  </div>
</section>
