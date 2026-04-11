<script lang="ts">
  import { onMount } from 'svelte';
  import gsap from 'gsap';
  import { ScrollTrigger } from 'gsap/dist/ScrollTrigger';
  import Typography from '../atoms/Typography.svelte';

  let heroContainer: HTMLElement;
  let svgBook: SVGSVGElement;
  let cover: SVGGElement;
  let floatingContainer: HTMLElement;

  const photos = [
    "https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1775930330/wyitikbqijztuqmajvu1.jpg",
    "https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1775930331/vndzhakwmene22rzhz7h.jpg",
    "https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1775930330/uxj92iubgljh5vt5vdmt.jpg",
    "https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1775930330/sek8u2lrrdniabrc1kku.jpg"
  ];

  let days = $state(0);
  let hours = $state(0);
  let minutes = $state(0);
  let seconds = $state(0);

  function updateCountdown() {
      const targetDate = new Date('2026-04-22T00:00:00').getTime();
      const now = new Date().getTime();
      const difference = targetDate - now;

      if (difference > 0) {
          days = Math.floor(difference / (1000 * 60 * 60 * 24));
          hours = Math.floor((difference % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
          minutes = Math.floor((difference % (1000 * 60 * 60)) / (1000 * 60));
          seconds = Math.floor((difference % (1000 * 60)) / 1000);
      }
  }

  onMount(() => {
    gsap.registerPlugin(ScrollTrigger);
    
    updateCountdown();
    const timer = setInterval(updateCountdown, 1000);

    const tl = gsap.timeline({
      scrollTrigger: {
        trigger: heroContainer,
        start: "top top",
        end: "+=320%", // Compacted scroll distance
        scrub: 1.2,
        pin: true,
        anticipatePin: 1
      }
    });

    gsap.set(svgBook, { transformOrigin: "50% 50%", force3D: true });
    
    const floatingImages = floatingContainer.querySelectorAll('.floating-photo');
    floatingImages.forEach((img, i) => {
        gsap.set(img, { 
            z: -2500, 
            autoAlpha: 0,
            scale: 0.1,
            xPercent: -50,
            yPercent: -50,
            left: "50%",
            top: "50%",
            rotationZ: i % 2 === 0 ? 4 : -4,
            force3D: true
        });
    });

    // 1. Initial Phase
    tl.to('.countdown-container', { autoAlpha: 0, y: -20, duration: 0.4 }, 0);

    // 2. Portal Phase
    tl.to(cover, {
      rotateY: -110,
      scaleX: 0.4,
      autoAlpha: 0,
      x: -150,
      duration: 1.2,
      ease: "power2.inOut"
    }, 0.1);

    tl.to(svgBook, {
      scale: 100,
      autoAlpha: 0,
      duration: 2,
      ease: "power2.in"
    }, 0.3);

    // 3. Sequential Transitions (Compact timing)
    floatingImages.forEach((img, i) => {
        const start = 1.8 + (i * 2.2); // First photo starts almost right after book explosion
        
        // Entry
        tl.fromTo(img, 
            { autoAlpha: 0, scale: 0.1, z: -2500, filter: "blur(10px)" },
            { 
                autoAlpha: 1, 
                scale: 1, 
                z: 0, 
                filter: "blur(0px)",
                duration: 1, 
                ease: "power3.out" 
            }, 
            start
        );

        // Zoom 
        tl.to(img, {
            scale: 1.2,
            z: 500,
            duration: 0.8,
            ease: "none"
        }, start + 1);

        // Exit
        tl.to(img, {
            autoAlpha: 0,
            z: 3500,
            scale: 10,
            filter: "blur(15px)",
            duration: 0.8,
            ease: "power2.in"
        }, start + 1.8);
    });

    // 4. CLEAN EXIT: Fade out hero to pass to section
    tl.to(heroContainer, { 
        autoAlpha: 0, 
        duration: 0.5 
    }, "=+0.2"); // Happens IMMEDIATELY after last photo exit

    return () => clearInterval(timer);
  });
</script>

<section 
  bind:this={heroContainer}
  class="relative h-screen w-full flex flex-col items-center justify-center bg-white overflow-hidden"
  style="perspective: 1500px;"
>
  <div class="absolute inset-0 opacity-[0.012] pointer-events-none bg-[url('https://www.transparenttextures.com/patterns/pinstriped-suit.png')] z-0"></div>

  <!-- Space for Floating Photos -->
  <div bind:this={floatingContainer} class="absolute inset-0 pointer-events-none z-40 flex items-center justify-center overflow-hidden" style="transform-style: preserve-3d;">
      {#each photos as url, i (url)}
        <div class="floating-photo absolute w-[320px] aspect-[1/1.2] bg-white rounded-sm shadow-2xl overflow-hidden will-change-transform border-[10px] border-white">
            <img src="{url}?auto=format&fit=crop&q=70&w=600" alt="Recuerdo" class="w-full h-full object-cover" />
        </div>
      {/each}
  </div>

  <!-- Countdown -->
  <div class="countdown-container absolute top-20 z-30 flex flex-col items-center gap-6 will-change-transform">
      <Typography tag="span" variant="caption" color="dark">EL VIAJE COMIENZA . . .</Typography>
      <div class="flex gap-12">
          {#each [{v: days, l: 'Días'}, {v: hours, l: 'Hrs'}, {v: minutes, l: 'Min'}, {v: seconds, l: 'Seg'}] as item, idx (idx)}
            <div class="flex flex-col items-center">
                <span class="text-5xl font-numbers text-dark tracking-tighter font-light">{item.v}</span>
                <span class="text-[9px] tracking-[4px] opacity-20 uppercase font-bold mt-2">{item.l}</span>
            </div>
          {/each}
      </div>
  </div>

  <!-- The Portal Book -->
  <div class="relative z-20 w-full h-full flex items-center justify-center pt-20 will-change-transform">
    <svg 
      bind:this={svgBook}
      viewBox="0 0 500 650" 
      class="w-[350px] h-[480px] overflow-visible"
      fill="none" 
      xmlns="http://www.w3.org/2000/svg"
    >
      <defs>
        <linearGradient id="bookBlack" x1="0%" y1="0%" x2="100%" y2="100%">
            <stop offset="0%" stop-color="#2a2a2a" />
            <stop offset="100%" stop-color="#000000" />
        </linearGradient>
        <linearGradient id="silverGradient" x1="0" y1="0" x2="100%" y2="100%">
            <stop offset="0%" stop-color="#ffffff" />
            <stop offset="100%" stop-color="#999999" />
        </linearGradient>
      </defs>
      <rect x="65" y="65" width="370" height="520" rx="10" fill="rgba(0,0,0,0.06)" />
      <rect x="50" y="50" width="400" height="550" rx="4" fill="#111111" />
      <g class="pages-thickness">
          {#each { length: 8 } as _, pageIdx (pageIdx)}
            <rect x={55 + (pageIdx*0.8)} y={55} width={390 - (pageIdx*1.5)} height={540} rx="2" fill="#fdfdfd" stroke="#f0f0f0" stroke-width="0.5" />
          {/each}
      </g>
      <g class="pages-content"><rect x="62" y="60" width="376" height="530" fill="white" /></g>
      <rect x="35" y="50" width="35" height="550" fill="#000000" rx="4" />
      <g bind:this={cover} class="origin-left">
          <rect x="50" y="50" width="400" height="550" rx="4" fill="url(#bookBlack)" />
          <rect x="75" y="75" width="350" height="500" rx="2" stroke="url(#silverGradient)" stroke-width="0.5" stroke-opacity="0.3" fill="none" />
          <g filter="drop-shadow(0 2px 4px rgba(0,0,0,0.5))">
              <text x="50%" y="42%" dominant-baseline="middle" text-anchor="middle" font-family="Playfair Display" font-size="28" fill="white" font-weight="bold" letter-spacing="8">M . A . F . E</text>
              <text x="50%" y="52%" dominant-baseline="middle" text-anchor="middle" font-family="Inter" font-size="12" fill="white" opacity="0.8" font-weight="600" letter-spacing="4">CUMPLEAÑOS # 28</text>
              <text x="50%" y="62%" dominant-baseline="middle" text-anchor="middle" font-family="Inter" font-size="8" fill="white" opacity="0.5" letter-spacing="8">22 DE ABRIL</text>
          </g>
      </g>
    </svg>
  </div>
</section>

<style>
  .will-change-transform {
    will-change: transform, opacity, filter;
    backface-visibility: hidden;
    transform: translateZ(0);
  }
  svg { overflow: visible !important; }
  .floating-photo {
    transform-style: preserve-3d;
    opacity: 0;
    visibility: hidden;
    filter: blur(10px);
  }
</style>
