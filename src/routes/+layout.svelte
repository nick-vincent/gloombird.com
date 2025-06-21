<script lang="ts">
	import { fade } from 'svelte/transition';
	import { goto } from '$app/navigation';

	import '$lib/styles/index.css';
	import Apple from '$lib/icons/apple.svelte';
	import Spotify from '$lib/icons/spotify.svelte';
	import YouTube from '$lib/icons/youtube.svelte';
	import Tidal from '$lib/icons/tidal.svelte';
	import Amazon from '$lib/icons/amazon.svelte';
	import Pandora from '$lib/icons/pandora.svelte';

	const songs = ['circles', 'float', 'landfill', 'luminate', 'twilight'];

	export let data;

	let audio: HTMLAudioElement;
	let duration: number;
	let currentTime: number;
	let playing = false;

	$: buttonText = playing ? 'Pause' : 'Play';
	$: song = data.pathname.replace(/\//g, '');
	$: progress = currentTime / duration || 0;

	const inTransition = {
		duration: 1000,
		delay: 1000
	};

	const outTransition = {
		duration: 1000
	};

	const goToNextSong = () => {
		const index = songs.indexOf(song);
		const nextIndex = (index + 1) % songs.length;
		song = songs[nextIndex];
		goto(`/${song}/`);
	};

	const capitalize = (str: string) => {
		return str.charAt(0).toUpperCase() + str.slice(1);
	};

	const toggleAudio = () => {
		if (playing) {
			pause();
		} else {
			play();
		}
	};

	const play = () => {
		audio.play();
		playing = true;
	};

	const pause = () => {
		audio.pause();
		playing = false;
	};
</script>

<main>
	<nav>
		<button class="toggle-audio" on:click={toggleAudio}>
			<span class={playing ? 'pause' : 'play'}></span>
			<span class="visually-hidden">{buttonText}</span>
		</button>

		<h1>Gloombird</h1>
		<ol>
			{#each songs as song}
				<li><a href={`/${song}/`} on:click={play}>{capitalize(song)}</a></li>
			{/each}
		</ol>

		<ul>
			<li><Apple /></li>
			<li><Spotify /></li>
			<li><Tidal /></li>
			<!-- <li><YouTube /></li> -->
			<li><Pandora /></li>
			<li><Amazon /></li>
		</ul>

		{#if song}
			<audio
				src={`/${song}.mp3`}
				autoplay
				bind:this={audio}
				bind:duration
				bind:currentTime
				on:ended={goToNextSong}
			/>
		{/if}

		<progress value={progress} />
	</nav>

	<div class="images">
		{#key song}
			<div in:fade={inTransition} out:fade={outTransition}>
				<slot />
			</div>
		{/key}
	</div>
</main>

<style>
	main {
		width: 100dvw;
		height: 100dvh;
	}

	nav {
		position: fixed;
		z-index: 1;
		inset: 0;
		top: auto;
		padding: 2rem;
		background-image: radial-gradient(
			farthest-side at 0 bottom,
			rgba(32, 32, 32, 0.9) 0%,
			transparent 100%
		);
		background-repeat: no-repeat;
	}

	.images {
		position: absolute;
		inset: 0;
		z-index: -1;
		aspect-ratio: 1;
		width: 100dvw;
		height: 100dvh;
	}

	.toggle-audio {
		display: flex;
		position: relative;
		cursor: pointer;
		border-radius: 100%;
		width: 3rem;
		height: 3rem;
		border: 0.25rem solid white;
		margin-bottom: 0.5rem;
	}

	.play {
		position: absolute;
		border: 0.5rem solid transparent;
		border-left: 0.75rem solid white;
		left: 50%;
		top: 50%;
		margin-left: 0.35rem;
		translate: -50% -50%;
	}

	.pause {
		position: absolute;
		border-right: 0.25rem solid white;
		border-left: 0.25rem solid white;
		height: 1rem;
		width: 0.75rem;
		left: 50%;
		top: 50%;
		translate: -50% -50%;
	}

	progress {
		appearance: none;
		width: 100%;
		height: 0.25rem;
		z-index: 1;
		position: fixed;
		bottom: 0;
		left: 0;
	}

	progress::-webkit-progress-bar {
		background-color: rgba(0, 0, 0, 0.5);
	}

	progress::-webkit-progress-value {
		background-color: white;
	}
</style>
