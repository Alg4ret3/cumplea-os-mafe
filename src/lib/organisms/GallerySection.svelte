<script lang="ts">
  import Typography from '../atoms/Typography.svelte';
  import ImageReveal from '../atoms/ImageReveal.svelte';
</script>

<section class="min-h-screen py-32 px-6 bg-white flex flex-col items-center">
  <div class="max-w-6xl w-full grid grid-cols-1 md:grid-cols-2 gap-12 md:gap-24">
    <div class="flex flex-col justify-center space-y-8">
      <Typography tag="h2" variant="title" color="dark">Momentos que guardo en el corazón</Typography>
      <div class="opacity-60">
        <Typography tag="p" variant="body" color="dark">
            Cada instante contigo es un regalo. En este día tan especial, quiero recordar lo afortunado que soy de tenerte.
        </Typography>
      </div>
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

    <div class="flex flex-col justify-center space-y-8">
      <div class="border-l-2 border-black/10 pl-6 py-4">
        <Typography tag="p" variant="subtitle" color="dark">
          "Gracias por cada sonrisa y por dejarme ser parte de tu historia."
        </Typography>
      </div>
      <div class="w-full h-px bg-black/10"></div>
    </div>
  </div>
</section>
