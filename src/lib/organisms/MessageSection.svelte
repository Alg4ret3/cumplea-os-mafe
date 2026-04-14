<script lang="ts">
	import { onMount } from 'svelte';
	import gsap from 'gsap';
	import { ScrollTrigger } from 'gsap/ScrollTrigger';

	gsap.registerPlugin(ScrollTrigger);

	let sectionRef: HTMLElement;
	let paperRef: HTMLElement;
	let cursorRef: HTMLElement | undefined;
	let lineContainers: (HTMLElement | undefined)[] = [];

	function splitIntoChars(text: string): string {
		return text
			.split('')
			.map((char) =>
				char === ' '
					? '<span class="char" style="opacity:0">&nbsp;</span>'
					: `<span class="char" style="opacity:0">${char}</span>`
			)
			.join('');
	}

	function getResponsiveValues() {
		const isMobile = typeof window !== 'undefined' && window.innerWidth < 640;
		return {
			scrollStart: isMobile ? 20 : 0,
			linesPerScroll: isMobile ? 100 : 78,
			duration: isMobile ? 0.03 : 0.05,
			yOffset: isMobile ? -12 : -18,
			wobbleAmount: isMobile ? 2 : 3
		};
	}

	onMount(() => {
		if (!paperRef || !cursorRef) return;

		const config = getResponsiveValues();

		gsap.set(paperRef, { opacity: 0, y: 20 });
		gsap.set(cursorRef, { opacity: 0, rotation: -15 });

		gsap.to(paperRef, {
			opacity: 1,
			y: 0,
			duration: 0.8,
			ease: 'power2.out',
			scrollTrigger: {
				trigger: sectionRef,
				start: 'top 60%',
				toggleActions: 'play none none reverse'
			}
		});

		gsap.to(cursorRef, {
			opacity: 1,
			duration: 0.2,
			scrollTrigger: {
				trigger: sectionRef,
				start: 'top 60%',
				toggleActions: 'play none none reverse'
			}
		});

		lineContainers.forEach((container, lineIndex) => {
			if (!container || !cursorRef) return;

			const chars = container.querySelectorAll('.char');
			const totalChars = chars.length;
			if (totalChars === 0) return;

			gsap.set(chars, { opacity: 0 });

			const linesPerIndex = config.linesPerScroll === 100 ? 8 : 11;
			const scrollStart = config.linesPerScroll - lineIndex * linesPerIndex + config.scrollStart;
			const scrollEnd = scrollStart - 20;

			let progressObj = { written: 0 };
			gsap.to(progressObj, {
				written: totalChars,
				duration: totalChars * config.duration,
				ease: 'none',
				scrollTrigger: {
					trigger: sectionRef,
					start: `top ${scrollStart}%`,
					end: `top ${scrollEnd}%`,
					scrub: 1,
					toggleActions: 'play reverse play reverse'
				},
				onUpdate: () => {
					const writtenCount = Math.floor(progressObj.written);

					chars.forEach((char, charIndex) => {
						const el = char as HTMLElement;
						el.style.opacity = charIndex < writtenCount ? '1' : '0';
					});

					if (writtenCount > 0 && writtenCount <= totalChars && cursorRef) {
						const currentChar = chars[Math.min(writtenCount - 1, totalChars - 1)] as HTMLElement;
						if (currentChar) {
							const rect = currentChar.getBoundingClientRect();
							const parentRect = paperRef.getBoundingClientRect();
							const progress = progressObj.written % 1;
							const x = rect.left - parentRect.left + rect.width;
							const y = rect.top - parentRect.top;
							const wobble = Math.sin(progress * Math.PI * 6) * config.wobbleAmount;
							gsap.set(cursorRef, {
								left: x + wobble,
								top: y + config.yOffset,
								rotation: -15 + wobble
							});
						}
					}
				}
			});
		});
	});
</script>

<section
	bind:this={sectionRef}
	class="flex min-h-screen items-center justify-center py-12 sm:py-24"
	style="perspective: 1500px; background: #f8f8f8;"
>
	<div
		class="pointer-events-none absolute inset-0 z-0 bg-[url('https://www.transparenttextures.com/patterns/pinstriped-suit.png')] opacity-[0.015]"
	></div>

	<div class="w-full max-w-lg px-3 sm:px-6">
		<h1
			class="mb-8 text-center text-2xl text-[#1a1a1a] sm:mb-16 sm:text-3xl"
			style="font-family: 'Playfair Display', serif;"
		>
			Cartas para ti
		</h1>

		<div bind:this={paperRef} class="card">
			<div class="sheet">
				<div bind:this={lineContainers[0]} class="line-container">
					<span class="greeting">{@html splitIntoChars('Querida Mafe,')}</span>
				</div>

				<div bind:this={lineContainers[1]} class="line-container">
					<span class="message"
						>{@html splitIntoChars(
							'Hoy es un día especial, no solo porque es tu cumpleaños, sino porque es una oportunidad para celebrar todo lo que sos y todo lo que representás para mí.'
						)}</span
					>
				</div>

				<div bind:this={lineContainers[2]} class="line-container">
					<span class="message"
						>{@html splitIntoChars(
							'Este año ha sido un viaje increíble a tu lado. Compartimos risas, momentos difíciles, aventuras y conversaciones que valoro más de lo que puedo expresar con palabras.'
						)}</span
					>
				</div>

				<div bind:this={lineContainers[3]} class="line-container">
					<span class="message"
						>{@html splitIntoChars(
							'Cada día aprendo algo nuevo de vos, cada momento contigo es un regalo que atesoro en mi corazón. Gracias por ser exactamente quien sos.'
						)}</span
					>
				</div>

				<div bind:this={lineContainers[4]} class="line-container">
					<span class="message"
						>{@html splitIntoChars(
							'Espero que este nuevo año de vida esté lleno de alegría, sorpresas y todo lo que merecés.'
						)}</span
					>
				</div>

				<div bind:this={lineContainers[5]} class="line-container">
					<div class="signature">
						<span>{@html splitIntoChars('Con todo mi cariño,')}</span>
						<span class="name">{@html splitIntoChars('Tu Sergi')}</span>
					</div>
				</div>

				<div bind:this={cursorRef} class="cursor">
					<svg class="pencil-icon" width="32" height="32" viewBox="0 0 24 24" fill="none">
						<path
							d="M3 14.25L17.81 3.06a2.85 2.85 0 0 1 4.03 4.03L9.25 20.25H3v-6l.25-.75z"
							fill="#f4a460"
							stroke="#8b4513"
							stroke-width="1.5"
							stroke-linecap="round"
							stroke-linejoin="round"
						/>
						<path
							d="M17.81 3.06L20.25 5.5"
							stroke="#8b4513"
							stroke-width="1.5"
							stroke-linecap="round"
						/>
						<path d="M14.5 6.75L6.75 14.5" stroke="#8b4513" stroke-width="1" />
						<path d="M11 17l1-1a2.85 2.85 0 0 1 4.03 4.03l-3.25 3.25" fill="#d4a574" />
					</svg>
				</div>
			</div>
		</div>
	</div>
</section>

<style>
	.card {
		perspective: 600px;
	}

	.sheet {
		position: relative;
		background: #fcfcfc;
		padding: 20px 20px 20px 40px;
		border-radius: 2px 6px 6px 2px;
		box-shadow:
			0 1px 3px rgba(0, 0, 0, 0.05),
			0 4px 16px rgba(0, 0, 0, 0.1);
		transform-style: preserve-3d;
		max-width: 100%;
		overflow: hidden;
	}

	@media (min-width: 640px) {
		.sheet {
			padding: clamp(24px, 5vw, 48px);
			padding-left: clamp(40px, 8vw, 60px);
		}
	}

	.sheet::before {
		content: '';
		position: absolute;
		left: 24px;
		top: 20px;
		bottom: 20px;
		width: 1px;
		background: repeating-linear-gradient(
			to bottom,
			transparent,
			transparent 24px,
			rgba(200, 60, 60, 0.2) 24px,
			rgba(200, 60, 60, 0.2) 25px
		);
	}

	@media (min-width: 640px) {
		.sheet::before {
			left: 36px;
		}
	}

	.line-container {
		display: block;
		margin-bottom: 12px;
		min-height: 1.6em;
	}

	@media (min-width: 640px) {
		.line-container {
			margin-bottom: 16px;
		}
	}

	.greeting {
		font-family: 'Caveat', cursive;
		font-size: 22px;
		color: #1a1a1a;
		display: block;
		margin-bottom: 20px;
	}

	@media (min-width: 640px) {
		.greeting {
			font-size: clamp(26px, 6vw, 32px);
			margin-bottom: 28px;
		}
	}

	.message {
		font-family: 'Caveat', cursive;
		font-size: 18px;
		line-height: 1.5;
		color: #333;
		display: block;
	}

	@media (min-width: 640px) {
		.message {
			font-size: clamp(20px, 4vw, 24px);
			line-height: 1.6;
		}
	}

	:global(.char) {
		display: inline !important;
	}

	.signature {
		margin-top: 24px;
		padding-top: 16px;
		border-top: 1px solid rgba(0, 0, 0, 0.08);
	}

	@media (min-width: 640px) {
		.signature {
			margin-top: 32px;
			padding-top: 20px;
		}
	}

	.signature span {
		font-family: 'Caveat', cursive;
		font-size: 18px;
		color: #555;
		display: block;
	}

	.signature .name {
		font-family: 'Caveat', cursive;
		font-size: 22px;
		color: #1a1a1a;
		margin-top: 4px;
	}

	@media (min-width: 640px) {
		.signature span {
			font-size: 20px;
		}
		.signature .name {
			font-size: 26px;
			margin-top: 6px;
		}
	}

	.cursor {
		position: absolute;
		pointer-events: none;
		z-index: 10;
		transform-origin: center;
		filter: drop-shadow(1px 1px 1px rgba(0, 0, 0, 0.2));
		width: 24px;
		height: 24px;
	}

	@media (min-width: 640px) {
		.cursor {
			width: 32px;
			height: 32px;
			filter: drop-shadow(2px 2px 2px rgba(0, 0, 0, 0.25));
		}
	}
</style>
