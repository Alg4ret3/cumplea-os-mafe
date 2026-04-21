<script lang="ts">
	import { onMount } from 'svelte';
	import gsap from 'gsap';
	import { ScrollTrigger } from 'gsap/dist/ScrollTrigger';
	import Typography from '../atoms/Typography.svelte';

	let sectionRef: HTMLElement;
	let containerRef: HTMLElement;

	const panels = [
		{
			image:
				'https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1776380880/aewv1lqcqoidflbknorc.png',
			title: 'Nuestro Primer Momento',
			text: 'Te paso a recordar un poco de como ha sido nuestra historia hasta ahora, y todo lo que hemos vivido juntos... Como olvidar nuestra primer foto juntos'
		},
		{
			image:
				'https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1776380879/ktrtpxkjbhfeqv91rus8.jpg',
			title: 'Fotos que no te gustan, pero a mi me encantan',
			text: 'Esa foto y muchas mas, me hacen dar cuenta de lo mucho que me gustas y lo feliz que soy a tu lado, y sobretodo de que las risas no faltan cuando estamos juntos'
		},
		{
			image:
				'https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1776380879/jxzgguyxwtuasgvpgccz.jpg',
			title: 'Logros y Triunfos',
			text: 'Verme lograr mis metes a tu lado me hace dar cuenta lo afortunado que soy en la vida, se muy bien que en tu logro no estoy contigo fiscamente, pero oye, no te olvides que donde quiera que estes, siempre estare, y de nuevo te lo digo, te felciito y eres una gran abogada, aunque peleas mucho jajaj',
			color: '#e8e8e8'
		},
		{
			image:
				'https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1776701623/be777mxow9ggcglmbv0r.jpg',
			title: 'Por Siempre',
			text: 'Hasta el momento hemos tenido experiencias gratificantes y se que esto apenas esta empezando, y que nos esperan muchas cosas buenas por vivir juntos, y de nuevo te lo digo,  feliz cumpleaños mi amor y recuerdalo, el camino es largo y lo mejor es que lo recorreremos juntos',
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
					<div class="panel-title">
						<Typography tag="h2" variant="subtitle" align="center">
							{panel.title}
						</Typography>
					</div>

					<!-- Descripción -->
					<div class="panel-text">
						<Typography tag="p" variant="body" color="gray" align="center">
							{panel.text}
						</Typography>
					</div>
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
		margin: 0;
		line-height: 1.2;
		font-family: 'Caveat', cursive;
		font-size: clamp(2rem, 6vw, 3rem);
		font-weight: 400;
		color: #1a1a1a;
	}

	.panel-text {
		max-width: 420px;
		margin: 0;
		line-height: 1.8;
		font-family: 'Caveat', cursive;
		font-size: clamp(1.25rem, 3vw, 1.5rem);
		color: #444444;
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
