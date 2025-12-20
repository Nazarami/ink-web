<script lang="ts">
	import { browser } from '$app/environment';
	import { goto } from '$app/navigation';
	import {
		PaintbrushVertical,
		Users,
		RotateCcw,
		Type,
		Clock,
		Copy,
		Play,
		ArrowLeft,
		Check,
		Crown
	} from '@lucide/svelte';
	import { Button } from '$lib/components/ui/button';
	import { Input } from '$lib/components/ui/input';
	import { Label } from '$lib/components/ui/label';
	import { Slider } from '$lib/components/ui/slider';

	// User state
	let username = $state('');
	let mounted = $state(false);
	let copied = $state(false);

	// Game settings
	let maxPlayers = $state(8);
	let rounds = $state(3);
	let wordCount = $state(3);
	let drawTime = $state(80);

	// Stub data for lobby
	const lobbyCode = 'ABC123';
	const inviteLink = $derived(`${browser ? window.location.origin : ''}/lobby/${lobbyCode}`);

	// Stub players list
	let players = $state([{ id: '1', name: 'You', isHost: true }]);

	// Stub activity log
	let activityLog = $state<{ id: string; message: string; timestamp: Date }[]>([
		{ id: '1', message: 'Lobby created', timestamp: new Date() }
	]);

	$effect(() => {
		if (browser) {
			if (!mounted) {
				username = (localStorage.getItem('ink-username') ?? 'Player').trim();
				if (username) {
					players[0].name = username;
				}
				mounted = true;
			}
		}
	});

	function handleCopyLink() {
		if (browser) {
			navigator.clipboard.writeText(inviteLink);
			copied = true;
			setTimeout(() => {
				copied = false;
			}, 2000);
		}
	}

	function handleStartGame() {
		// Stub: Would start the game
		console.log('Starting game with settings:', { maxPlayers, rounds, wordCount, drawTime });
		goto('/play');
	}

	function handleBack() {
		goto('/');
	}

	// Stub: Simulate a player joining (for demo purposes)
	function simulatePlayerJoin() {
		const names = ['Alice', 'Bob', 'Charlie', 'Diana', 'Eve', 'Frank'];
		const randomName = names[Math.floor(Math.random() * names.length)];
		const newPlayer = {
			id: crypto.randomUUID(),
			name: randomName,
			isHost: false
		};
		players = [...players, newPlayer];
		activityLog = [
			...activityLog,
			{
				id: crypto.randomUUID(),
				message: `${randomName} joined the lobby`,
				timestamp: new Date()
			}
		];
	}
</script>

<svelte:head>
	<title>Create Lobby - Ink</title>
</svelte:head>

<section class="min-h-svh p-4 md:p-8" aria-label="Create Lobby">
	<div class="mx-auto max-w-5xl transition-opacity duration-200" class:opacity-0={!mounted}>
		<!-- Header -->
		<header class="mb-8 flex items-center justify-between">
			<div class="flex items-center gap-4">
				<Button
					type="button"
					variant="ghost"
					size="icon"
					onclick={handleBack}
					class="text-slate-400 hover:text-slate-100"
					aria-label="Go back"
				>
					<ArrowLeft class="h-5 w-5" />
				</Button>
				<div class="flex items-center gap-2 select-none">
					<PaintbrushVertical class="h-8 w-8 text-purple-400" aria-hidden="true" />
					<h1 class="text-2xl font-bold">Create Lobby</h1>
				</div>
			</div>
			<div
				class="flex items-center gap-2 rounded-lg border border-slate-700 bg-slate-800/50 px-4 py-2"
			>
				<span class="text-sm text-slate-400">Code:</span>
				<span class="font-mono text-lg font-bold text-purple-400">{lobbyCode}</span>
			</div>
		</header>

		<div class="grid gap-6 lg:grid-cols-3">
			<!-- Left Column: Game Settings -->
			<div class="space-y-6 lg:col-span-2">
				<!-- Game Settings Card -->
				<div class="rounded-xl border border-slate-700 bg-slate-800/30 p-6">
					<h2 class="mb-6 text-lg font-semibold text-slate-100">Game Settings</h2>

					<div class="grid gap-6 sm:grid-cols-2">
						<!-- Max Players -->
						<div class="space-y-3">
							<div class="flex items-center justify-between">
								<Label class="flex items-center gap-2 text-slate-300">
									<Users class="h-4 w-4 text-purple-400" />
									Max Players
								</Label>
								<span class="text-sm font-medium text-purple-400">{maxPlayers}</span>
							</div>
							<Slider
								type="single"
								bind:value={maxPlayers}
								min={2}
								max={16}
								step={1}
								class="slider-custom"
							/>
							<div class="flex justify-between text-xs text-slate-500">
								<span>2</span>
								<span>16</span>
							</div>
						</div>

						<!-- Rounds -->
						<div class="space-y-3">
							<div class="flex items-center justify-between">
								<Label class="flex items-center gap-2 text-slate-300">
									<RotateCcw class="h-4 w-4 text-purple-400" />
									Rounds
								</Label>
								<span class="text-sm font-medium text-purple-400">{rounds}</span>
							</div>
							<Slider
								type="single"
								bind:value={rounds}
								min={1}
								max={10}
								step={1}
								class="slider-custom"
							/>
							<div class="flex justify-between text-xs text-slate-500">
								<span>1</span>
								<span>10</span>
							</div>
						</div>

						<!-- Word Count -->
						<div class="space-y-3">
							<div class="flex items-center justify-between">
								<Label class="flex items-center gap-2 text-slate-300">
									<Type class="h-4 w-4 text-purple-400" />
									Words to Choose
								</Label>
								<span class="text-sm font-medium text-purple-400">{wordCount}</span>
							</div>
							<Slider
								type="single"
								bind:value={wordCount}
								min={1}
								max={5}
								step={1}
								class="slider-custom"
							/>
							<div class="flex justify-between text-xs text-slate-500">
								<span>1</span>
								<span>5</span>
							</div>
						</div>

						<!-- Draw Time -->
						<div class="space-y-3">
							<div class="flex items-center justify-between">
								<Label class="flex items-center gap-2 text-slate-300">
									<Clock class="h-4 w-4 text-purple-400" />
									Draw Time
								</Label>
								<span class="text-sm font-medium text-purple-400">{drawTime}s</span>
							</div>
							<Slider
								type="single"
								bind:value={drawTime}
								min={30}
								max={180}
								step={10}
								class="slider-custom"
							/>
							<div class="flex justify-between text-xs text-slate-500">
								<span>30s</span>
								<span>180s</span>
							</div>
						</div>
					</div>
				</div>

				<!-- Activity Log Card -->
				<div class="rounded-xl border border-slate-700 bg-slate-800/30 p-6">
					<h2 class="mb-4 text-lg font-semibold text-slate-100">Activity Log</h2>
					<div
						class="h-40 space-y-2 overflow-y-auto rounded-lg border border-slate-700/50 bg-slate-900/50 p-3"
					>
						{#each activityLog as log (log.id)}
							<div class="flex items-start gap-2 text-sm">
								<span class="text-slate-500">
									{log.timestamp.toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' })}
								</span>
								<span class="text-slate-300">{log.message}</span>
							</div>
						{:else}
							<p class="text-sm text-slate-500">No activity yet...</p>
						{/each}
					</div>
				</div>
			</div>

			<!-- Right Column: Players & Actions -->
			<div class="space-y-6">
				<!-- Invite Link Card -->
				<div class="rounded-xl border border-slate-700 bg-slate-800/30 p-6">
					<h2 class="mb-4 text-lg font-semibold text-slate-100">Invite Friends</h2>
					<div class="flex gap-2">
						<Input
							type="text"
							value={inviteLink}
							readonly
							class="border-slate-700 bg-slate-900/50 text-sm text-slate-300"
						/>
						<Button
							type="button"
							variant="none"
							size="icon"
							onclick={handleCopyLink}
							class="shrink-0 border border-slate-600 bg-slate-800/50 text-slate-100 hover:border-purple-500 hover:bg-purple-600/20"
							aria-label="Copy invite link"
						>
							{#if copied}
								<Check class="h-4 w-4 text-green-400" />
							{:else}
								<Copy class="h-4 w-4" />
							{/if}
						</Button>
					</div>
					{#if copied}
						<p class="mt-2 text-xs text-green-400">Link copied to clipboard!</p>
					{/if}
				</div>

				<!-- Players Card -->
				<div class="rounded-xl border border-slate-700 bg-slate-800/30 p-6">
					<div class="mb-4 flex items-center justify-between">
						<h2 class="text-lg font-semibold text-slate-100">Players</h2>
						<span
							class="rounded-full bg-purple-500/20 px-2 py-0.5 text-xs font-medium text-purple-400"
						>
							{players.length}/{maxPlayers}
						</span>
					</div>
					<ul class="space-y-2">
						{#each players as player (player.id)}
							<li
								class="flex items-center gap-3 rounded-lg border border-slate-700/50 bg-slate-900/50 px-3 py-2"
							>
								<div
									class="flex h-8 w-8 items-center justify-center rounded-full bg-purple-500/20 text-sm font-medium text-purple-400"
								>
									{player.name.charAt(0).toUpperCase()}
								</div>
								<span class="flex-1 text-sm text-slate-200">{player.name}</span>
								{#if player.isHost}
									<Crown class="h-4 w-4 text-yellow-400" aria-label="Host" />
								{/if}
							</li>
						{/each}
					</ul>

					<!-- Demo button to simulate player joining -->
					<Button
						type="button"
						variant="ghost"
						size="sm"
						onclick={simulatePlayerJoin}
						disabled={players.length >= maxPlayers}
						class="mt-4 w-full text-xs text-slate-500 hover:text-slate-300"
					>
						+ Simulate player join (demo)
					</Button>
				</div>

				<!-- Start Game Button -->
				<Button
					type="button"
					variant="none"
					size="lg"
					onclick={handleStartGame}
					disabled={players.length < 2}
					class="h-14 w-full bg-purple-600 text-lg font-semibold text-white shadow-lg hover:bg-purple-500 focus-visible:ring-purple-500/50 disabled:bg-slate-700 disabled:text-slate-400"
				>
					<Play class="h-6 w-6" aria-hidden="true" />
					Start Game
				</Button>
				{#if players.length < 2}
					<p class="text-center text-xs text-slate-500">Need at least 2 players to start</p>
				{/if}
			</div>
		</div>
	</div>
</section>

<style>
	:global(.slider-custom [data-slot='slider-track']) {
		height: 8px;
		background: rgb(51 65 85 / 0.5);
	}

	:global(.slider-custom [data-slot='slider-range']) {
		background: rgb(168 85 247);
	}

	:global(.slider-custom [data-slot='slider-thumb']) {
		width: 18px;
		height: 18px;
		background: rgb(168 85 247);
		border: none;
		box-shadow: 0 2px 4px rgb(0 0 0 / 0.3);
	}

	:global(.slider-custom [data-slot='slider-thumb']:hover) {
		background: rgb(192 132 252);
	}

	:global(.slider-custom [data-slot='slider-thumb']:focus-visible) {
		box-shadow: 0 0 0 3px rgb(168 85 247 / 0.5);
	}
</style>
