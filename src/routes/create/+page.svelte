<script lang="ts">
	import { untrack } from 'svelte';
	import { browser } from '$app/environment';

	let username = $state('');

	$effect(() => {
		if (browser) {
			untrack(() => {
				username = localStorage.getItem('ink-username') ?? '';
			});
		}
	});
</script>

<svelte:head>
	<title>Create Lobby - Ink</title>
</svelte:head>

<section class="flex min-h-svh flex-col items-center justify-center gap-4">
	<h1 class="text-4xl font-bold">Create Lobby</h1>
	{#if username}
		<p class="text-slate-400">Welcome, <span class="text-slate-100">{username}</span></p>
	{/if}
</section>
