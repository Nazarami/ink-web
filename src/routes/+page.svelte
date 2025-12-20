<script lang="ts">
	import { untrack } from 'svelte';
	import { goto } from '$app/navigation';
	import { browser } from '$app/environment';
	import { PaintbrushVertical, User, Play, CirclePlus, Hash } from '@lucide/svelte';
	import { Button } from '$lib/components/ui/button';
	import { Input } from '$lib/components/ui/input';
	import { Label } from '$lib/components/ui/label';

	let username = $state('');
	let mounted = $state(false);

	$effect(() => {
		if (browser) {
			untrack(() => {
				username = localStorage.getItem('ink-username') ?? '';
			});
			mounted = true;
		}
	});

	$effect(() => {
		if (mounted) {
			localStorage.setItem('ink-username', username);
		}
	});

	function handlePlayNow() {
		goto('/play');
	}

	function handleSubmit(e: Event) {
		e.preventDefault();
		handlePlayNow();
	}
</script>

<svelte:head>
	<title>Ink - Draw & Guess Together</title>
</svelte:head>

<section class="flex min-h-svh items-center justify-center" aria-label="Welcome">
	<div
		class="flex flex-col items-center gap-6 transition-opacity duration-200"
		class:opacity-0={!mounted}
	>
		<header class="flex items-center gap-2">
			<PaintbrushVertical class="h-12 w-12 text-purple-400" aria-hidden="true" />
			<h1 class="text-5xl font-bold">Ink</h1>
		</header>

		<form class="w-full max-w-sm" onsubmit={handleSubmit}>
			<Label for="username" class="mb-2 block text-slate-400">Your Name</Label>
			<div class="relative">
				<div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3">
					<User class="h-5 w-5 text-slate-400" aria-hidden="true" />
				</div>
				<Input
					type="text"
					id="username"
					name="username"
					bind:value={username}
					placeholder="Enter your name"
					maxlength={20}
					autocomplete="username"
					required
					aria-required="true"
					aria-describedby={username.length > 0 ? 'username-count' : undefined}
					class="h-12 border-slate-700 bg-slate-800/50 pl-10 text-slate-100 placeholder:text-slate-500 focus-visible:border-purple-500 focus-visible:ring-purple-500/50"
				/>
			</div>
			<div
				class="grid transition-all duration-300 ease-in-out"
				class:grid-rows-[1fr]={username.length > 0}
				class:grid-rows-[0fr]={username.length === 0}
			>
				<div class="overflow-hidden">
					<p id="username-count" class="mt-1 text-xs text-slate-400" aria-live="polite">
						{username.length}/20 characters
					</p>
				</div>
			</div>
		</form>

		<nav class="flex w-full max-w-sm flex-col gap-3" aria-label="Game options">
			<Button
				type="button"
				variant="none"
				size="lg"
				disabled={username.length === 0}
				onclick={handlePlayNow}
				class="h-12 bg-purple-600 font-semibold text-white shadow-md hover:bg-purple-500 focus-visible:ring-purple-500/50"
			>
				<Play class="h-5 w-5" aria-hidden="true" />
				Play Now
			</Button>
			<Button
				type="button"
				variant="none"
				size="lg"
				disabled={username.length === 0}
				class="h-11 border border-slate-600 bg-slate-800/50 text-slate-100 hover:border-slate-500 hover:bg-slate-700/50 focus-visible:ring-purple-500/50"
			>
				<CirclePlus class="h-5 w-5" aria-hidden="true" />
				Create Lobby
			</Button>
			<Button
				type="button"
				variant="none"
				size="lg"
				disabled={username.length === 0}
				class="h-11 border border-slate-600 bg-slate-800/50 text-slate-100 hover:border-slate-500 hover:bg-slate-700/50 focus-visible:ring-purple-500/50"
			>
				<Hash class="h-5 w-5" aria-hidden="true" />
				Join with Code
			</Button>
		</nav>
	</div>
</section>
