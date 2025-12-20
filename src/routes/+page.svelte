<script lang="ts">
	import { goto } from '$app/navigation';
	import { browser } from '$app/environment';
	import { PaintbrushVertical, User, Play, CirclePlus, Hash } from '@lucide/svelte';
	import { Button } from '$lib/components/ui/button';
	import { Input } from '$lib/components/ui/input';
	import { Label } from '$lib/components/ui/label';
	import * as Dialog from '$lib/components/ui/dialog';

	let username = $state('');
	let mounted = $state(false);
	let lobbyCode = $state('');
	let joinDialogOpen = $state(false);

	$effect(() => {
		if (browser) {
			if (!mounted) {
				username = (localStorage.getItem('ink-username') ?? '').trim();
				mounted = true;
			} else {
				localStorage.setItem('ink-username', username.trimEnd());
			}
		}
	});

	function handleUsernameInput(e: Event) {
		const input = e.target as HTMLInputElement;
		// Remove leading whitespace and collapse multiple spaces into one
		input.value = input.value.trimStart().replace(/  +/g, ' ');
		username = input.value;
	}

	function handlePlayNow() {
		goto('/play');
	}

	function handleCreateLobby() {
		goto('/lobby/create');
	}

	function handleJoinLobby(e: Event) {
		e.preventDefault();
		if (lobbyCode.trim()) {
			goto(`/lobby/${lobbyCode.trim()}`);
		}
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
		<header class="flex items-center gap-2 select-none">
			<PaintbrushVertical class="h-12 w-12 text-purple-400" aria-hidden="true" />
			<h1 class="text-5xl font-bold">Ink</h1>
		</header>

		<form class="w-full max-w-sm" onsubmit={handleSubmit}>
			<div class="mb-2 flex items-center justify-between">
				<Label for="username" class="block text-slate-400">Your Name</Label>
				<p
					id="username-count"
					class="text-xs text-slate-500 transition-opacity duration-150 select-none"
					class:opacity-0={username.length === 0}
					aria-live="polite"
				>
					{username.length}/20
				</p>
			</div>
			<div class="relative">
				<div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3">
					<User class="h-5 w-5 text-slate-400" aria-hidden="true" />
				</div>
				<Input
					type="text"
					id="username"
					name="username"
					value={username}
					oninput={handleUsernameInput}
					placeholder="Enter your name"
					maxlength={20}
					autocomplete="username"
					required
					aria-required="true"
					aria-describedby={username.length > 0 ? 'username-count' : undefined}
					class="h-12 border-slate-700 bg-slate-800/50 pl-10 text-slate-100 placeholder:text-slate-500 focus-visible:border-purple-500 focus-visible:ring-purple-500/50"
				/>
			</div>
		</form>

		<nav class="flex w-full max-w-sm flex-col gap-3 select-none" aria-label="Game options">
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
				onclick={handleCreateLobby}
				class="h-11 border border-slate-600 bg-slate-800/50 text-slate-100 hover:border-slate-500 hover:bg-slate-700/50 focus-visible:ring-purple-500/50"
			>
				<CirclePlus class="h-5 w-5" aria-hidden="true" />
				Create Lobby
			</Button>
			<Dialog.Root bind:open={joinDialogOpen}>
				<Dialog.Trigger
					disabled={username.length === 0}
					class="inline-flex h-11 shrink-0 items-center justify-center gap-2 rounded-md border border-slate-600 bg-slate-800/50 px-6 text-sm font-medium text-slate-100 transition-all outline-none hover:border-slate-500 hover:bg-slate-700/50 focus-visible:ring-[3px] focus-visible:ring-purple-500/50 disabled:pointer-events-none disabled:opacity-50"
				>
					<Hash class="h-5 w-5" aria-hidden="true" />
					Join with Code
				</Dialog.Trigger>
				<Dialog.Content>
					<Dialog.Header>
						<Dialog.Title>Join a Lobby</Dialog.Title>
						<Dialog.Description>Enter the lobby code to join your friends.</Dialog.Description>
					</Dialog.Header>
					<form onsubmit={handleJoinLobby} class="space-y-4">
						<div>
							<Label for="lobby-code" class="mb-2 block text-slate-300">Lobby Code</Label>
							<Input
								type="text"
								id="lobby-code"
								bind:value={lobbyCode}
								placeholder="Enter code"
								maxlength={10}
								class="border-slate-700 bg-slate-800/50 text-slate-100 placeholder:text-slate-500 focus-visible:border-purple-500 focus-visible:ring-purple-500/50"
							/>
						</div>
						<Dialog.Footer>
							<Button
								type="submit"
								variant="none"
								disabled={lobbyCode.trim().length === 0}
								class="bg-purple-600 font-semibold text-white hover:bg-purple-500 focus-visible:ring-purple-500/50"
							>
								Join Lobby
							</Button>
						</Dialog.Footer>
					</form>
				</Dialog.Content>
			</Dialog.Root>
		</nav>
	</div>
</section>
