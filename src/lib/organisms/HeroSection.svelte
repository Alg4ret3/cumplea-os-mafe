<script lang="ts">
	import { onMount } from 'svelte';
	import gsap from 'gsap';
	import { ScrollTrigger } from 'gsap/dist/ScrollTrigger';
	import confetti from 'canvas-confetti';
	import Typography from '../atoms/Typography.svelte';

	let heroContainer: HTMLElement;
	let bookWrapper: HTMLDivElement;
	let bookCover: HTMLDivElement | null = null;
	let bookPages: HTMLDivElement | null = null;

	const photos = [
		{
			url: 'https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1775930330/wyitikbqijztuqmajvu1.jpg',
			text: 'En este dia tan especial, mi corazón esta que estalla de felicidad al saber que ...'
		},
		{
			url: 'https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1775930331/vndzhakwmene22rzhz7h.jpg',
			text: 'No solamente es tu cumpleaños, tambien aquieres un nuevo logro a tu carrera profesional...'
		},
		{
			url: 'https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1775930330/uxj92iubgljh5vt5vdmt.jpg',
			text: 'No hay palabras para describir lo orgulloso y afortunado que soy de tenerte en mi vida, pero ...'
		},
		{
			url: 'https://res.cloudinary.com/dqky6oqrd/image/upload/f_auto,q_auto/v1775930330/sek8u2lrrdniabrc1kku.jpg',
			text: 'Quiero que sepas, que todo esto entre los dos, apenas esta empezando, y esta sera la primera de muchas aventuras juntos...'
		},
		{
			url: null,
			text: 'Con muchas cosas por decir, pero no hay palabras para describir lo que siento por ti, feliz cumpleaños mi corachon y que Dios te bendiga siempre',
			footer: '17/ 04 / 2026'
		}
	];

	let days = $state(0);
	let hours = $state(0);
	let minutes = $state(0);
	let seconds = $state(0);
	let countdownFinished = $state(false);
	// Variable GLOBAL para bloquear audio
	(window as any).countdownFinished = false;

	function triggerConfetti() {
		// Explosión principal
		confetti({
			particleCount: 150,
			spread: 80,
			origin: { y: 0.6 }
		});

		// Explosiones laterales
		setTimeout(() => {
			confetti({
				particleCount: 80,
				angle: 60,
				spread: 55,
				origin: { x: 0 }
			});
		}, 200);

		setTimeout(() => {
			confetti({
				particleCount: 80,
				angle: 120,
				spread: 55,
				origin: { x: 1 }
			});
		}, 400);

		// Repetir 2 veces mas
		setTimeout(() => confetti({ particleCount: 100, spread: 100 }), 600);
		setTimeout(() => confetti({ particleCount: 100, spread: 100 }), 1000);

		// LLUVIA DE CORAZONES
		for (let i = 0; i < 8; i++) {
			setTimeout(() => {
				confetti({
					particleCount: 8,
					angle: 90,
					spread: 120,
					origin: { y: 0, x: Math.random() },
					emoji: '❤️',
					scalar: 1.2,
					gravity: 0.6,
					drift: 0
				});
			}, i * 400);
		}
	}

	function updateCountdown() {
		const targetDate = new Date('2026-04-21T17:45:00-05:00').getTime();
		const now = new Date().getTime();
		const difference = targetDate - now;

		if (difference > 0) {
			days = Math.floor(difference / (1000 * 60 * 60 * 24));
			hours = Math.floor((difference % (1000 * 60 * 60 * 24)) / (1000 * 60 * 60));
			minutes = Math.floor((difference % (1000 * 60 * 60)) / (1000 * 60));
			seconds = Math.floor((difference % (1000 * 60)) / 1000);
			// Bloquear scroll
			document.body.style.overflow = 'hidden';
		} else if (!countdownFinished) {
			countdownFinished = true;
			(window as any).countdownFinished = true;
			triggerConfetti();
			// DESBLOQUEAR SCROLL TOTALMENTE
			document.body.style.overflow = '';
			document.body.style.position = '';
			document.body.style.width = '';
			document.body.style.top = '';
			document.body.style.touchAction = '';
		}
	}

	onMount(() => {
		gsap.registerPlugin(ScrollTrigger);

		// BLOQUEO TOTAL DE SCROLL DESDE EL PRINCIPIO
		document.body.style.overflow = 'hidden';
		document.body.style.position = 'fixed';
		document.body.style.width = '100%';
		document.body.style.top = '0';
		document.body.style.touchAction = 'none';
		window.scrollTo(0, 0);

		updateCountdown();
		const timer = setInterval(updateCountdown, 1000);

		const tl = gsap.timeline({
			scrollTrigger: {
				trigger: heroContainer,
				start: 'top top',
				end: '+=500%',
				scrub: true,
				pin: true,
				anticipatePin: 1
			}
		});

		// 1. Initial Phase - Hide countdown
		tl.to('.countdown-container', { autoAlpha: 0, y: -20, duration: 0.4 }, 0);

		// 2. Book Opening Animation
		if (bookCover) {
			tl.to(bookCover, { rotateY: -120, x: -100, duration: 1.5, ease: 'power2.inOut' }, 0.2);
		}

		if (bookPages) {
			tl.to(bookPages, { opacity: 1, duration: 0.8, ease: 'power2.out' }, 0.5);
		}

		// 3. Page flip animation - 5 pages (page 0 visible initially)
		const pageSheets = bookPages?.querySelectorAll('.page-sheet') as NodeListOf<HTMLElement>;

		// Set initial states
		pageSheets?.forEach((page, i) => {
			if (i === 0) {
				gsap.set(page, { rotationY: 0, transformOrigin: 'left center', opacity: 1, force3D: true });
			} else {
				gsap.set(page, { rotationY: 0, transformOrigin: 'left center', opacity: 0, force3D: true });
			}
		});

		// Animate all 4 pages: pages 1, 2, 3 flip to reveal next page
		// Page 0 flips at the very end to show the back
		pageSheets?.forEach((page, i) => {
			const start = 2 + i * 2;

			// Make page visible (except page 0 which starts visible)
			if (i > 0) {
				tl.to(page, { opacity: 1, duration: 0.5, force3D: true }, start - 0.5);
			}

			// Flip page to left
			tl.to(
				page,
				{
					rotationY: -90,
					duration: 1.5,
					ease: 'power2.inOut',
					force3D: true
				},
				start
			);
		});

		// 4. Close the book
		if (bookPages) {
			tl.to(bookPages, { opacity: 0, duration: 1 }, '+=1');
		}
		if (bookCover) {
			tl.to(bookCover, { rotateY: 0, x: 0, duration: 1.2, ease: 'power2.inOut' }, '-=0.8');
		}

		// 5. Very subtle fade - no black flash
		tl.to('.book-wrapper', { opacity: 0, duration: 0.8 }, '+=0.5');

		return () => {
			clearInterval(timer);
			// Limpiar TODOS los estilos de bloqueo al desmontar
			document.body.style.overflow = '';
			document.body.style.position = '';
			document.body.style.width = '';
			document.body.style.top = '';
			document.body.style.touchAction = '';
		};
	});
</script>

<section
	bind:this={heroContainer}
	class="relative flex h-screen w-full flex-col items-center justify-center overflow-hidden"
	style="perspective: 1500px; background: #f8f8f8;"
>
	<div
		class="pointer-events-none absolute inset-0 z-0 bg-[url('https://www.transparenttextures.com/patterns/pinstriped-suit.png')] opacity-[0.015]"
	></div>

	<!-- Countdown -->
	<div
		class="countdown-container absolute top-20 z-30 flex flex-col items-center gap-6 will-change-transform"
	>
		{#if !countdownFinished}
			<Typography tag="span" variant="caption" color="dark">EL VIAJE COMIENZA . . .</Typography>

			<div class="flex gap-12">
				{#each [{ v: days, l: 'Días' }, { v: hours, l: 'Hrs' }, { v: minutes, l: 'Min' }, { v: seconds, l: 'Seg' }] as item, idx (idx)}
					<div class="flex flex-col items-center">
						<span class="font-numbers text-5xl font-light tracking-tighter text-dark">{item.v}</span
						>
						<span class="mt-2 text-[9px] font-bold tracking-[4px] uppercase opacity-20"
							>{item.l}</span
						>
					</div>
				{/each}
			</div>
		{:else}
			<div class="text-center transition-all duration-1000 ease-out">
				<Typography tag="span" variant="caption" color="dark">
					¡ FELIZ CUMPLEAÑOS MI CORACHON !
				</Typography>
			</div>
		{/if}
	</div>

	<!-- The Portal Book - 3D CSS Version -->
	<div
		bind:this={bookWrapper}
		class="book-wrapper relative z-20 flex h-full w-full items-center justify-center pt-20"
		style="perspective: 2000px; transform-style: preserve-3d;"
	>
		<div class="book-container" style="transform-style: preserve-3d;">
			<!-- Back Cover -->
			<div
				class="back-cover"
				style="
				position: absolute;
				width: 350px;
				height: 480px;
				background: linear-gradient(135deg, #111 0%, #222 100%);
				border-radius: 4px;
				transform: translateZ(-2px);
				box-shadow: 0 0 0 1px rgba(255,255,255,0.1);
			"
			></div>

			<!-- Pages Block with page flip effect -->
			<div
				bind:this={bookPages}
				class="book-pages"
				style="
					position: absolute;
					width: 340px;
					height: 470px;
					opacity: 0;
				"
			>
				<!-- Each page is a flippable sheet with front and back -->
				<div class="pages-stack">
					{#each photos as photo, i (photo.url || i)}
						<div class="page-sheet" data-page={i} style="z-index: {photos.length - i};">
							<!-- Front of page (photo + text) -->
							<div class="page-front">
								<div class="page-paper">
									<div class="page-content">
										{#if photo.url}
											<div class="photo-image">
												<img src="{photo.url}?auto=format&fit=crop&q=70&w=600" alt="Recuerdo" />
											</div>
										{:else}
											<div class="final-message">
												<p>{photo.text}</p>
												{#if photo.footer}
													<p class="final-footer">{photo.footer}</p>
												{/if}
											</div>
										{/if}
										{#if photo.url}
											<div class="photo-text">
												<p>{photo.text}</p>
											</div>
										{/if}
									</div>
								</div>
							</div>
							<!-- Back of page (visible when flipped to left) -->
							<div class="page-back">
								<div class="page-back-content">
									<span class="page-number">{i + 1}</span>
								</div>
							</div>
						</div>
					{/each}
				</div>
			</div>

			<!-- Front Cover -->
			<div
				bind:this={bookCover}
				class="front-cover"
				style="
					position: absolute;
					width: 350px;
					height: 480px;
					background: linear-gradient(180deg, #0a0a0a 0%, #1a1a1a 50%, #0a0a0a 100%);
					border-radius: 4px;
					transform-origin: left center;
					transform: translateZ(2px);
					box-shadow: 6px 6px 25px rgba(0,0,0,0.6);
					display: flex;
					flex-direction: column;
					align-items: center;
					justify-content: center;
					gap: 12px;
					border: 1px solid rgba(255,255,255,0.1);
					overflow: hidden;
				"
			>
				<!-- Decorative geometric pattern -->
				<div style="position: absolute; inset: 0; opacity: 0.08;">
					<svg width="100%" height="100%" xmlns="http://www.w3.org/2000/svg">
						<defs>
							<pattern
								id="geometric"
								x="0"
								y="0"
								width="40"
								height="40"
								patternUnits="userSpaceOnUse"
							>
								<circle cx="20" cy="20" r="2" fill="white" />
								<path d="M0 20h40M20 0v40" stroke="white" stroke-width="0.5" opacity="0.5" />
								<rect
									x="0"
									y="0"
									width="40"
									height="40"
									fill="none"
									stroke="white"
									stroke-width="0.3"
								/>
							</pattern>
						</defs>
						<rect width="100%" height="100%" fill="url(#geometric)" />
					</svg>
				</div>
				<!-- Decorative corners -->
				<div
					style="position: absolute; top: 20px; left: 20px; width: 50px; height: 50px; border-top: 1px solid rgba(255,255,255,0.3); border-left: 1px solid rgba(255,255,255,0.3);"
				></div>
				<div
					style="position: absolute; top: 20px; right: 20px; width: 50px; height: 50px; border-top: 1px solid rgba(255,255,255,0.3); border-right: 1px solid rgba(255,255,255,0.3);"
				></div>
				<div
					style="position: absolute; bottom: 20px; left: 20px; width: 50px; height: 50px; border-bottom: 1px solid rgba(255,255,255,0.3); border-left: 1px solid rgba(255,255,255,0.3);"
				></div>
				<div
					style="position: absolute; bottom: 20px; right: 20px; width: 50px; height: 50px; border-bottom: 1px solid rgba(255,255,255,0.3); border-right: 1px solid rgba(255,255,255,0.3);"
				></div>
				<span
					style="font-family: 'Playfair Display', serif; font-size: 32px; color: white; font-weight: bold; letter-spacing: 6px;"
					>MI BEBE</span
				>
				<span
					style="font-family: 'Inter', sans-serif; font-size: 14px; color: white; opacity: 0.9; font-weight: 600; letter-spacing: 6px;"
					>FELIZ CUMPLEAÑOS</span
				>
				<span
					style="font-family: 'Playfair Display', serif; font-size: 24px; color: white; font-weight: bold;"
					># 28</span
				>
				<span
					style="font-family: 'Inter', sans-serif; font-size: 10px; color: white; opacity: 0.6; letter-spacing: 10px; margin-top: 10px;"
					>22 - 04 - 1998
				</span>
			</div>
		</div>
	</div>
</section>

<style>
	@keyframes fadeInUp {
		0% {
			opacity: 0;
			transform: translateY(30px);
		}
		100% {
			opacity: 1;
			transform: translateY(0);
		}
	}

	.will-change-transform {
		will-change: transform, opacity, filter;
		backface-visibility: hidden;
		transform-style: preserve-3d;
	}
	.book-wrapper {
		will-change: transform, opacity;
	}
	.book-container {
		position: relative;
		width: 350px;
		height: 480px;
		transform-style: preserve-3d;
	}
	.book-pages {
		transform-style: preserve-3d;
		perspective: 1500px;
	}
	.book-pages,
	.front-cover,
	.back-cover {
		backface-visibility: hidden;
		-webkit-backface-visibility: hidden;
	}
	.pages-stack {
		position: relative;
		width: 100%;
		height: 100%;
		transform-style: preserve-3d;
	}
	.page-sheet {
		position: absolute;
		inset: 0;
		transform-style: preserve-3d;
		backface-visibility: hidden;
		-webkit-backface-visibility: hidden;
	}
	.page-sheet[data-page='0'] {
		opacity: 1;
	}
	.page-front,
	.page-back {
		position: absolute;
		inset: 0;
		backface-visibility: hidden;
		-webkit-backface-visibility: hidden;
	}
	.page-front {
		transform: rotateY(0deg);
		z-index: 1;
	}
	.page-back {
		transform: rotateY(180deg);
		background: linear-gradient(
			90deg,
			#e0e0e0 0%,
			#f5f5f5 20%,
			#fff 50%,
			#f5f5f5 80%,
			#e0e0e0 100%
		);
		border-radius: 0 3px 3px 0;
		box-shadow: inset -5px 0 15px rgba(0, 0, 0, 0.05);
		display: flex;
		align-items: center;
		justify-content: center;
	}
	.page-back-content {
		text-align: center;
	}
	.page-number {
		font-family: 'Caveat', cursive;
		font-size: 48px;
		color: #bbb;
		opacity: 0.5;
	}
	.page-paper {
		position: absolute;
		inset: 0;
		background: linear-gradient(
			90deg,
			#e8e8e8 0%,
			#fefefe 8%,
			#fff 15%,
			#fff 85%,
			#f8f8f8 92%,
			#e0e0e0 100%
		);
		box-shadow:
			inset -8px 0 20px rgba(0, 0, 0, 0.08),
			inset 3px 0 8px rgba(0, 0, 0, 0.03);
		border-radius: 0 3px 3px 0;
		transform-style: preserve-3d;
	}
	.page-content {
		position: relative;
		width: 100%;
		height: 100%;
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		gap: 25px;
		padding: 30px;
	}
	.photo-image {
		width: 200px;
		aspect-ratio: 1 / 1.3;
		overflow: hidden;
		border: 6px solid white;
		background: white;
		box-shadow: 2px 3px 8px rgba(0, 0, 0, 0.15);
		transform: rotate(-1deg);
	}
	.photo-image img {
		width: 100%;
		height: 100%;
		object-fit: cover;
	}
	.photo-text {
		padding: 0 20px;
	}
	.photo-text p {
		font-family: 'Caveat', 'Dancing Script', cursive, serif;
		font-size: 20px;
		color: #444;
		line-height: 1.5;
		text-align: center;
		font-style: italic;
	}
	.final-message {
		display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center;
		height: 100%;
	}
	.final-message p {
		font-family: 'Caveat', cursive;
		font-size: 32px;
		color: #000000;
		text-align: center;
		line-height: 1.4;
	}
	.final-footer {
		font-family: 'Caveat', cursive;
		font-size: 14px;
		color: #888;
		margin-top: 80px;
		letter-spacing: 2px;
	}
</style>
