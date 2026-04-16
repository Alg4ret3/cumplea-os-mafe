<script lang="ts">
	import { onMount } from 'svelte';
	import gsap from 'gsap';
	import { ScrollTrigger } from 'gsap/dist/ScrollTrigger';

	let sectionRef: HTMLElement;
	let containerRef: HTMLElement;

	const panels = [
		{
			image:
				'https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1776380880/aewv1lqcqoidflbknorc.png',
			title: 'Nuestro Primer Momento',
			text: 'El día que nuestras vidas cambiaron para siempre. Un instante que marcó el inicio de nuestra historia juntos.',
			color: '#f8f8f8'
		},
		{
			image:
				'https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1776380879/ktrtpxkjbhfeqv91rus8.jpg',
			title: 'Momentos Inolvidables',
			text: 'Cada risa, cada conversación, cada silencio compartido construye lo que somos. Gracias por estar ahí.',
			color: '#f0f0f0'
		},
		{
			image:
				'https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1776380879/jxzgguyxwtuasgvpgccz.jpg',
			title: 'Aventuras Juntos',
			text: 'Los viajes, las bromas, los errores y los logros. Todo lo que vivimos es nuestro tesoro más valioso.',
			color: '#e8e8e8'
		},
		{
			image:
				'https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1776380879/yfcsmz84eanuudddvalh.jpg',
			title: 'Por Siempre',
			text: 'Te amo más de lo que las palabras pueden expresar. Mi presente y mi futuro son contigo. Feliz cumpleaños.',
			color: '#e0e0e0'
		}
	];

	onMount(() => {
		gsap.registerPlugin(ScrollTrigger);

		// Scroll horizontal principal
		gsap.to(containerRef, {
			xPercent: -300,
			ease: 'none',
			scrollTrigger: {
				trigger: sectionRef,
				start: 'top top',
				end: () => `+=${containerRef.scrollWidth - window.innerWidth}`,
				scrub: 1,
				pin: true,
				pinSpacing: true,
				invalidateOnRefresh: true
			}
		});

		// Animaciones por panel
		panels.forEach((_, i) => {
			const panel = document.querySelector<HTMLElement>(`.panel[data-index="${i}"]`);
			const title = panel?.querySelector<HTMLElement>('.panel-title');
			const text = panel?.querySelector<HTMLElement>('.panel-text');

			if (!panel) return;

			// ANIMACIÓN DE TÍTULO
			if (title) {
				gsap.fromTo(
					title,
					{ opacity: 0, y: 40, clipPath: 'inset(0 0 100% 0)' },
					{
						opacity: 1,
						y: 0,
						clipPath: 'inset(0 0 0% 0)',
						duration: 1,
						delay: 0.2,
						ease: 'power3.out',
						scrollTrigger: {
							trigger: panel,
							start: 'left 75%',
							toggleActions: 'play none none reverse'
						}
					}
				);
			}

			// ANIMACIÓN DE TEXTO
			if (text) {
				gsap.fromTo(
					text,
					{ opacity: 0, y: 30 },
					{
						opacity: 1,
						y: 0,
						duration: 0.9,
						delay: 0.4,
						ease: 'power2.out',
						scrollTrigger: {
							trigger: panel,
							start: 'left 70%',
							toggleActions: 'play none none reverse'
						}
					}
				);
			}
		});

		return () => ScrollTrigger.getAll().forEach((t) => t.kill());
	});

	// ✅ REVELADO DE COLOR POR MOUSE
	function revealColor(e: MouseEvent, imageEl: HTMLDivElement) {
		const rect = imageEl.getBoundingClientRect();
		const x = e.clientX - rect.left;
		const y = e.clientY - rect.top;

		// Radio del área revelada
		const radius = 120;

		// APLICAMOS EL CLIP-PATH A LA CAPA DE COLOR, NO AL CONTENEDOR
		const colorLayer = imageEl.querySelector('.image-color') as HTMLElement;
		if (colorLayer) {
			colorLayer.style.clipPath = `circle(${radius}px at ${x}px ${y}px)`;
		}
	}

	function resetImage(imageEl: HTMLDivElement) {
		// Volvemos a ocultar completamente la capa de color
		const colorLayer = imageEl.querySelector('.image-color') as HTMLElement;
		if (colorLayer) {
			colorLayer.style.clipPath = 'circle(0px at 50% 50%)';
		}
	}
</script>

<section bind:this={sectionRef} class="horizontal-section">
	<div bind:this={containerRef} class="panels-container">
		{#each panels as panel, index}
			<article class="panel" style="background: {panel.color}" data-index={index}>
				<div class="panel-inner">
					<!-- ✅ IMAGEN CON REVELADO DE COLOR POR MOUSE -->
					<div
						class="panel-image"
						on:mousemove={(e) => revealColor(e, e.currentTarget)}
						on:mouseleave={(e) => resetImage(e.currentTarget)}
					>
						<!-- Imagen en blanco y negro (fondo) -->
						<img class="image-bw" src={panel.image} alt={panel.title} loading="lazy" />

						<!-- Imagen a color (superposición) -->
						<div class="image-color">
							<img src={panel.image} alt={panel.title} loading="lazy" />
						</div>
					</div>

					<!-- Título -->
					<h2 class="panel-title">{panel.title}</h2>

					<!-- Descripción -->
					<p class="panel-text">{panel.text}</p>
				</div>
			</article>
		{/each}
	</div>
</section>

<style>
	.horizontal-section {
		width: 100%;
		position: relative;
		overflow: hidden;
	}

	.panels-container {
		display: flex;
		height: 100vh;
		will-change: transform;
	}

	.panel {
		width: 100vw;
		height: 100vh;
		flex-shrink: 0;
		display: flex;
		align-items: center;
		justify-content: center;
	}

	.panel-inner {
		display: flex;
		flex-direction: column;
		align-items: center;
		gap: 2rem;
		max-width: 600px;
		padding: 1.5rem;
		text-align: center;
	}

	/* ==============================================
	   EFECTO REVELADO DE COLOR POR MOUSE
	   ============================================== */
	.panel-image {
		position: relative;
		width: 100%;
		max-width: 320px;
		aspect-ratio: 3/4;
		border-radius: 12px;
		overflow: hidden;
		box-shadow: 0 25px 60px rgba(0, 0, 0, 0.15);
		transform: rotate(-2deg);
		transition: transform 0.4s cubic-bezier(0.3, 0, 0.3, 1);
		cursor: crosshair;
	}

	.panel-image:hover {
		transform: rotate(0deg) scale(1.03);
		box-shadow: 0 30px 70px rgba(0, 0, 0, 0.2);
	}

	/* Imagen base: BLANCO Y NEGRO */
	.image-bw {
		position: absolute;
		inset: 0;
		width: 100%;
		height: 100%;
		object-fit: cover;
		filter: grayscale(100%);
	}

	/* Imagen superpuesta: COLOR */
	.image-color {
		position: absolute;
		inset: 0;
		width: 100%;
		height: 100%;
		clip-path: circle(0px at 50% 50%);
		transition: clip-path 0.1s ease-out;
		pointer-events: none;
	}

	.image-color img {
		width: 100%;
		height: 100%;
		object-fit: cover;
		filter: grayscale(0%);
	}

	/* ==============================================
	   TEXTO
	   ============================================== */
	.panel-title {
		font-family: 'Inter', sans-serif;
		font-size: clamp(1.25rem, 5vw, 2rem);
		font-weight: 400;
		letter-spacing: 0.2em;
		text-transform: uppercase;
		color: #1a1a1a;
		margin: 0;
		line-height: 1.4;
	}

	.panel-text {
		font-family: 'Inter', sans-serif;
		font-size: clamp(0.875rem, 3vw, 1rem);
		font-weight: 300;
		letter-spacing: 0.1em;
		color: #666666;
		line-height: 1.8;
		max-width: 420px;
		margin: 0;
	}

	/* ==============================================
	   RESPONSIVE
	   ============================================== */
	@media (max-width: 768px) {
		.panel-inner {
			gap: 1.75rem;
		}

		.panel-image {
			max-width: 260px;
			transform: rotate(-1deg);
		}

		.panel-title {
			font-size: clamp(1.75rem, 7vw, 2.5rem);
		}

		.panel-text {
			font-size: clamp(0.95rem, 3.5vw, 1.15rem);
		}
	}
</style>
