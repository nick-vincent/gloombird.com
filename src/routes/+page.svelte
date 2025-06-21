<script lang="ts">
	import { goto } from '$app/navigation';
	import { onMount } from 'svelte';

	onMount(() => {
		goto('/circles/', { replaceState: true });
	});
</script>

<svelte:head>
	<title>Gloombird</title>
	<meta name="description" content="Stream Luminate by Gloombird" />
	<meta property="og:title" content="Gloombird: Luminate" />
	<meta property="og:description" content="Stream Luminate by Gloombird" />
	<meta property="og:url" content="https://gloombird.com" />
	<meta
		property="og:image"
		content="https://gloombird.com/_app/immutable/assets/luminate.9IjqeyQu.png"
	/>
</svelte:head>
