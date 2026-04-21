<script lang="ts">
	import { getContext, onMount } from 'svelte';

	let playing = $state(false);
	let text = $state('');
	let i = $state(0);
	let currentTime = $state(0);
	let duration = $state(0);
	let bars = $state(Array(25).fill(8));
	let musicPlayer: any;

	const AUDIO_URL =
		'https://res.cloudinary.com/dqky6oqrd/video/upload/v1776726711/epzxph5ndz6fogcmwwd1.mp3';

	onMount(() => {
		// Obtener referencia al music player global
		musicPlayer = getContext('musicPlayer');
	});
	const MESSAGE = `Hola, mi corazón:

No sabía o mejor dicho, no sé muy bien cómo empezar. Tengo tantas cosas en la cabeza y en el corazón que me cuesta ponerlas en palabras, pero voy a intentarlo.

Ya estamos a pocos meses de cumplir un año desde que nos conocimos, y sinceramente, todo este tiempo contigo ha sido una de las cosas más bonitas que me han pasado en la vida. No lo digo solo en lo personal, sino en todos los aspectos; incluso en lo profesional has estado ahí, siempre con paciencia y con esa forma tan tuya de intentar entenderme. Créeme que lo valoro muchísimo.

Siento que hay muchas cosas guardadas en mi corazón cosas buenas que no siempre logro expresar, tal vez porque no soy muy bueno abriéndome, pero eso no significa que no las sienta.

Quiero que tengas presente algo: si hay algo que nunca te va a faltar conmigo, es el agradecimiento. De mí hacia ti siempre van a salir palabras sinceras de gratitud. Gracias por tu tiempo, por tu amor y por todo lo que me has dado.

Este detalle lo hice por dos razones muy especiales:
La primera, porque hoy celebramos tu cumpleaños número 28.
La segunda, porque acabas de lograr algo enorme: tu especialización.

Son cosas de las que deberías sentirte profundamente orgullosa. Has logrado muchísimo, aunque a veces siento que no dimensionas lo valiosa que eres ni todo lo que has alcanzado. Pero desde mi perspectiva, cada día pienso qué increíble es tener a una persona tan responsable, juiciosa y, sobre todo, tan visionaria en lo que quiere.

Eres una hija increíble y una profesional que se destaca, sin discusión. Y aunque quisiera decir mucho más, hay cosas que simplemente siento y no logro explicar.

Todo esto lo hice con un solo propósito: que te sientas querida, que sientas lo importante que eres para mí. Desde el primer día te lo dije: para mí, lo más valioso son esas cosas que no se pueden comprar, como la paz y la tranquilidad que tú me das. Y eso, la verdad, no lo cambiaría por nada.

Aún nos queda muchísimo camino por recorrer, y me hace feliz saber que quiero recorrerlo contigo.

Hoy solo quiero desearte un feliz cumpleaños. Espero que llegues a Pasto y podamos celebrar juntos este logro tan importante.

Te quiero, y quiero que sepas que siempre vas a tener un lugar muy especial en mi corazón.`;

	let audio: HTMLAudioElement;
	let writeInterval: any;
	let timeInterval: any;

	function updateTime() {
		currentTime = audio.currentTime;

		// Actualizar onda sonora en tiempo real
		for (let x = 0; x < bars.length; x++) {
			bars[x] = playing ? 8 + Math.random() * 32 : 8;
		}

		// Escribir texto EN TIEMPO REAL sincronizado con el audio
		if (currentTime > 2 && playing) {
			const progress = (currentTime - 2) / (duration - 3);
			const charPos = Math.floor(progress * MESSAGE.length);
			text = MESSAGE.slice(0, Math.min(charPos, MESSAGE.length));
		}
	}

	function fadeOutAndPause() {
		let fadeVolume = audio.volume;
		const fadeInterval: number = setInterval(() => {
			fadeVolume -= 0.05;
			if (fadeVolume <= 0) {
				clearInterval(fadeInterval);
				audio.pause();
				audio.volume = 1; // Restaurar volumen para proxima reproduccion
				playing = false;
				clearInterval(timeInterval);
				bars = Array(25).fill(8);
			} else {
				audio.volume = fadeVolume;
			}
		}, 50); // 1 segundo total de degradado (20 pasos * 50ms)
	}

	function togglePlay() {
		if (playing) {
			fadeOutAndPause();
			if ((window as any).musicPlayer) (window as any).musicPlayer.restore();
		} else {
			if ((window as any).musicPlayer) (window as any).musicPlayer.mute();
			audio.volume = 1;
			audio.play();
			playing = true;
			timeInterval = setInterval(updateTime, 100);
		}
	}

	function skip(seconds: number) {
		audio.currentTime += seconds;
		// Actualizar texto a la posicion exacta del audio
		const progress = audio.currentTime / duration;
		const charPos = Math.floor(progress * MESSAGE.length);
		text = MESSAGE.slice(0, charPos);
		i = charPos;
	}

	function restart() {
		audio.currentTime = 0;
		text = '';
		i = 0;
		currentTime = 0;
	}

	function audioEnded() {
		playing = false;
		clearInterval(timeInterval);
		bars = Array(25).fill(8);
		text = MESSAGE;
		// Devolver musica de fondo cuando termine el mensaje solo
		if ((window as any).musicPlayer) (window as any).musicPlayer.restore();
	}

	function loaded() {
		duration = audio.duration;
	}
</script>

<section class="flex min-h-screen w-full items-center justify-center bg-white px-6 py-20">
	<div class="mx-auto w-full max-w-2xl text-center">
		<h2 class="mb-16 text-4xl font-light tracking-wide md:text-5xl">Mi Mensaje Para Ti</h2>

		<!-- Reproductor Minimalista -->
		<div class="mb-12">
			<!-- Onda sonora -->
			<div class="mb-8 flex h-16 items-center justify-center gap-1">
				{#each bars as bar, index}
					<div
						class="w-0.5 rounded-full bg-gray-900 transition-all duration-75"
						style="height: {bar}px; opacity: {bar > 12 ? 1 : 0.3}"
					/>
				{/each}
			</div>

			<!-- Controles -->
			<div class="flex items-center justify-center gap-8">
				<button
					on:click={() => skip(-5)}
					class="p-3 opacity-60 transition-opacity hover:opacity-100"
				>
					<svg
						class="h-5 w-5"
						fill="none"
						stroke="currentColor"
						stroke-width="1.5"
						viewBox="0 0 24 24"
					>
						<path
							d="M12.066 11.2a1 1 0 000 1.6l5.334 4A1 1 0 0019 16V8a1 1 0 00-1.6-.8l-5.334 4zM4.066 11.2a1 1 0 000 1.6l5.334 4A1 1 0 0011 16V8a1 1 0 00-1.6-.8l-5.334 4z"
						/>
					</svg>
				</button>

				<button
					on:click={togglePlay}
					class="flex h-14 w-14 items-center justify-center rounded-full bg-gray-900 p-5 text-white transition-colors hover:bg-gray-800"
				>
					{#if playing}
						<svg class="h-5 w-5" fill="currentColor" viewBox="0 0 24 24">
							<rect x="7" y="5" width="3" height="14" rx="0.5" />
							<rect x="14" y="5" width="3" height="14" rx="0.5" />
						</svg>
					{:else}
						<svg class="ml-0.5 h-5 w-5" fill="currentColor" viewBox="0 0 24 24">
							<path d="M9 5l10 7-10 7V5z" />
						</svg>
					{/if}
				</button>

				<button
					on:click={() => skip(5)}
					class="p-3 opacity-60 transition-opacity hover:opacity-100"
				>
					<svg
						class="h-5 w-5"
						fill="none"
						stroke="currentColor"
						stroke-width="1.5"
						viewBox="0 0 24 24"
					>
						<path
							d="M11.933 12.8a1 1 0 000-1.6L6.6 7.2A1 1 0 005 8v8a1 1 0 001.6.8l5.333-4zM19.933 12.8a1 1 0 000-1.6l-5.333-4A1 1 0 0013 8v8a1 1 0 001.6.8l5.333-4z"
						/>
					</svg>
				</button>
			</div>

			<!-- Tiempo -->
			<div class="mt-6 text-xs tracking-widest text-gray-400 uppercase">
				{Math.floor(currentTime / 60)}:{String(Math.floor(currentTime % 60)).padStart(2, '0')}
			</div>
		</div>

		<!-- Texto -->
		<div class="min-h-[320px]">
			<p
				class="text-left text-gray-800"
				style="font-family: Caveat, cursive; font-size: 1.65rem; line-height: 1.7;"
			>
				{text}
				{#if playing && currentTime > 2}<span class="animate-pulse">|</span>{/if}
			</p>
		</div>

		<audio
			bind:this={audio}
			src={AUDIO_URL}
			preload="auto"
			on:loadedmetadata={loaded}
			on:ended={audioEnded}
		/>
	</div>
</section>
