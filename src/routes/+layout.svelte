<script lang="ts">
	import { fade } from 'svelte/transition';
	import { goto } from '$app/navigation';

	import '$lib/styles/index.css';
	import Apple from '$lib/icons/apple.svelte';
	import Spotify from '$lib/icons/spotify.svelte';
	import YouTube from '$lib/icons/youtube.svelte';
	import Tidal from '$lib/icons/tidal.svelte';
	import Amazon from '$lib/icons/amazon.svelte';
	import { onMount } from 'svelte';

	const songs = ['circles', 'float', 'landfill', 'luminate', 'twilight'];

	export let data;

	let audio: HTMLAudioElement;
	let duration: number;
	let currentTime: number;
	let playing = false;

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

	const play = () => {
		audio.play();
		playing = true;
	};
</script>

<main>
	<nav>
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
			<li><YouTube /></li>
			<li><Amazon /></li>
		</ul>

		<audio
			src={`/${song}.mp3`}
			autoplay
			bind:this={audio}
			bind:duration
			bind:currentTime
			on:ended={goToNextSong}
		/>

		<progress value={progress} />
	</nav>

	{#if !playing}
		<button class="play" on:click={play} out:fade={outTransition}>
			<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100"
				><path
					d="M50,2.3C23.7,2.3,2.3,23.7,2.3,50S23.7,97.7,50,97.7S97.7,76.3,97.7,50S76.3,2.3,50,2.3z M50,89.2   c-21.6,0-39.2-17.6-39.2-39.2S28.4,10.8,50,10.8S89.2,28.4,89.2,50S71.6,89.2,50,89.2z M41.3,31.1c-1.1-0.6-2.5,0.2-2.5,1.4   l-0.1,34.9c0,1.3,1.4,2.1,2.5,1.5l30.3-17.4c1.1-0.6,1.1-2.2,0-2.9L41.3,31.1z"
				/>
			</svg>
			<span class="visually-hidden">Play</span>
		</button>
	{/if}

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

	.play {
		position: absolute;
		display: flex;
		z-index: 0;
		inset: 0;
		cursor: pointer;
		background: rgba(0, 0, 0, 0.5);
	}

	.play svg {
		display: block;
		width: 10rem;
		height: 10rem;
		margin: auto;
		fill: rgba(255, 255, 255, 0.5);
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
