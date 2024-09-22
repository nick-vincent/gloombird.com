<script lang="ts">
	import { fade } from 'svelte/transition';
	import '$lib/styles/index.css';

	const imageModules = import.meta.glob('../lib/images/*.png', {
		eager: true,
		query: {
			enhanced: true
		}
	});

	console.log(imageModules);

	const songs = ['circles', 'float', 'landfill', 'luminate', 'twilight'];

	let slug: string;
	let duration: number;
	let currentTime: number;

	$: progress = currentTime / duration || 0;

	const inTransition = {
		duration: 1000,
		delay: 1000
	};

	const outTransition = {
		duration: 1000
	};

	const goToNextSong = () => {
		const index = songs.indexOf(slug);
		const nextIndex = (index + 1) % songs.length;
		slug = songs[nextIndex];
	};

	const capitalize = (str: string) => {
		return str.charAt(0).toUpperCase() + str.slice(1);
	};
</script>

<main>
	<nav>
		<h1>Gloombird</h1>
		<ul>
			{#each songs as song}
				<li><button on:click={() => (slug = song)}>{capitalize(song)}</button></li>
			{/each}
		</ul>
		<progress value={progress} />
	</nav>

	{#if slug}
		<audio src={`/${slug}.mp3`} autoplay bind:duration bind:currentTime on:ended={goToNextSong} />
	{/if}

	<div class="background">
    {#each Object.entries(imageModules) as [path, module]}
			<enhanced:img
				class="image"
				class:active={path.includes(slug)}
				src={module.default}
				in:fade={inTransition}
				out:fade={outTransition}
			/>
		{/each}
	</div>
</main>

<style>
	nav {
		position: fixed;
		padding: 2rem 1.5rem 2rem;
		left: 0;
		bottom: 0;
		right: 0;
		background: linear-gradient(to bottom, rgba(0, 0, 0, 0), rgba(0, 0, 0, 0.5));
		text-shadow: 0 0 0.5rem rgba(0, 0, 0, 0.25);
	}

	h1 {
		font-size: 1.5rem;
		margin: 0 0 0.5rem 0;
		padding: 0;
		letter-spacing: -0.025em;
	}

	ul {
		display: flex;
		margin: 0;
		padding: 0;
		gap: 1rem;
	}

	li {
		display: block;
		margin: 0;
		padding: 0;
	}

	button {
		font-family: inherit;
		border: none;
		font-size: inherit;
		background: none;
		color: white;
		display: block;
		margin: 0;
		padding: 0;
		z-index: 1;
		text-decoration: none;
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

	.background {
		position: absolute;
		inset: 0;
		z-index: -1;
	}

	.image {
		position: absolute;
		min-width: 100dvw;
		min-height: 100dvh;
		width: 100%;
		height: auto;
		object-fit: cover;
    opacity: 0;
    transition: opacity 1s;
	}

  .image.active {
    opacity: 1;
    transition: opacity 1s 1s;
  }
</style>
