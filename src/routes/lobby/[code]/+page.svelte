<script lang="ts">
	import { page } from '$app/stores';
	import { browser } from '$app/environment';
	import { goto } from '$app/navigation';
	import {
		PaintbrushVertical,
		Send,
		Clock,
		Users,
		Crown,
		Pencil,
		ArrowLeft,
		Eraser,
		Palette,
		Undo2,
		Trash2
	} from '@lucide/svelte';
	import { Button } from '$lib/components/ui/button';
	import { Input } from '$lib/components/ui/input';

	// Get lobby code from URL
	const lobbyCode = $derived($page.params.code);

	// User state
	let username = $state('');
	let mounted = $state(false);

	// Chat state
	let chatInput = $state('');
	let chatMessages = $state<{ id: string; sender: string; message: string; isSystem?: boolean }[]>([
		{ id: '1', sender: '', message: 'Game started!', isSystem: true },
		{ id: '2', sender: 'Alice', message: 'Good luck everyone!' },
		{ id: '3', sender: 'Bob', message: 'Is it food?' },
		{ id: '4', sender: '', message: 'Bob guessed correctly!', isSystem: true }
	]);

	// Game state
	let timeRemaining = $state(65);
	let currentWord = 'spaghetti';
	let currentDrawer = 'Alice';
	let round = $state(2);
	let totalRounds = 3;

	// Players
	let players = $state([
		{ id: '1', name: 'Alice', score: 450, isDrawing: true },
		{ id: '2', name: 'Bob', score: 320, hasGuessed: true },
		{ id: '3', name: 'Charlie', score: 280, hasGuessed: false },
		{ id: '4', name: 'You', score: 150, hasGuessed: false }
	]);

	// Canvas state
	let selectedColor = $state('#a855f7');
	let brushSize = $state(4);
	let isEraser = $state(false);
	const colors = [
		'#ffffff',
		'#000000',
		'#ef4444',
		'#f97316',
		'#eab308',
		'#22c55e',
		'#3b82f6',
		'#a855f7',
		'#ec4899'
	];

	// Chat container ref for auto-scroll
	let chatContainer: HTMLDivElement;

	$effect(() => {
		if (browser) {
			if (!mounted) {
				username = (localStorage.getItem('ink-username') ?? 'Player').trim();
				// Update "You" to actual username
				const youPlayer = players.find((p) => p.name === 'You');
				if (youPlayer && username) {
					youPlayer.name = username;
				}
				mounted = true;
			}
		}
	});

	// Auto-scroll chat to bottom when new messages arrive
	$effect(() => {
		if (chatContainer && chatMessages.length) {
			chatContainer.scrollTop = chatContainer.scrollHeight;
		}
	});

	function handleSendMessage(e: Event) {
		e.preventDefault();
		if (!chatInput.trim()) return;

		const newMessage = {
			id: crypto.randomUUID(),
			sender: username || 'You',
			message: chatInput.trim()
		};

		chatMessages = [...chatMessages, newMessage];
		chatInput = '';
	}

	function handleBack() {
		goto('/');
	}

	function selectColor(color: string) {
		selectedColor = color;
		isEraser = false;
	}

	function toggleEraser() {
		isEraser = !isEraser;
	}

	function clearCanvas() {
		// Stub: would clear the canvas
		console.log('Clear canvas');
	}

	function undoStroke() {
		// Stub: would undo last stroke
		console.log('Undo stroke');
	}

	// Check if current user is drawing
	let isDrawing = $derived(players.find((p) => p.name === username)?.isDrawing ?? false);

	// Masked word for guessers
	let maskedWord = $derived(
		isDrawing
			? currentWord
			: currentWord
					.split('')
					.map((c) => (c === ' ' ? '  ' : '_'))
					.join(' ')
	);
</script>

<svelte:head>
	<title>Lobby {lobbyCode} - Ink</title>
</svelte:head>

<section
	class="flex min-h-svh flex-col p-4 md:h-svh md:overflow-hidden md:p-6 lg:p-8"
	aria-label="Game Lobby"
>
	<div
		class="mx-auto flex w-full max-w-7xl flex-col transition-opacity duration-200 md:h-full"
		class:opacity-0={!mounted}
	>
		<!-- Header -->
		<header class="mb-5 flex items-center justify-between md:mb-4">
			<div class="flex items-center gap-2 md:gap-4">
				<Button
					type="button"
					variant="ghost"
					size="icon-sm"
					onclick={handleBack}
					class="text-slate-400 hover:text-slate-100"
					aria-label="Leave game"
				>
					<ArrowLeft class="h-4 w-4" />
				</Button>
				<div class="flex items-center gap-2 select-none">
					<PaintbrushVertical class="h-6 w-6 text-purple-400" aria-hidden="true" />
					<span class="text-lg font-bold">Ink</span>
				</div>
			</div>

			<!-- Round & Timer -->
			<div class="flex items-center gap-3 md:gap-6">
				<div class="text-sm text-slate-400">
					Round <span class="font-semibold text-slate-100">{round}/{totalRounds}</span>
				</div>
				<div
					class="flex items-center gap-2 rounded-lg border border-slate-700 bg-slate-800/50 px-3 py-1.5"
				>
					<Clock class="h-4 w-4 text-purple-400" />
					<span
						class="font-mono text-lg font-bold"
						class:text-red-400={timeRemaining <= 10}
						class:text-yellow-400={timeRemaining > 10 && timeRemaining <= 30}
						class:text-slate-100={timeRemaining > 30}
					>
						{timeRemaining}
					</span>
				</div>
			</div>

			<!-- Lobby Code -->
			<div
				class="hidden items-center gap-2 rounded-lg border border-slate-700 bg-slate-800/50 px-3 py-1.5 sm:flex"
			>
				<span class="text-xs text-slate-400">Code:</span>
				<span class="font-mono text-sm font-bold text-purple-400">{lobbyCode}</span>
			</div>
		</header>

		<!-- Word Display -->
		<div class="mb-4 text-center md:mb-3">
			<div
				class="inline-flex flex-col items-center gap-1 rounded-xl border border-slate-700 bg-slate-800/30 px-5 py-3 md:flex-row md:gap-3"
			>
				{#if isDrawing}
					<div class="flex items-center gap-2">
						<Pencil class="h-4 w-4 text-purple-400" />
						<span class="text-sm text-slate-400">Draw:</span>
					</div>
					<span class="font-mono text-xl font-bold tracking-wider text-purple-400 md:text-lg"
						>{currentWord}</span
					>
				{:else}
					<span class="text-xs text-slate-500 md:text-sm md:text-slate-400">Guess</span>
					<span
						class="font-mono text-base font-bold tracking-[0.25em] text-slate-100 md:text-lg md:tracking-[0.3em]"
						>{maskedWord}</span
					>
					<span class="text-xs text-slate-500">({currentWord.length} letters)</span>
				{/if}
			</div>
		</div>

		<!-- Players Bar -->
		<div
			class="mb-4 flex items-center gap-3 overflow-x-auto rounded-xl border border-slate-700 bg-slate-800/30 p-3 md:mb-3"
		>
			<div class="flex shrink-0 items-center gap-2">
				<Users class="h-4 w-4 text-purple-400" />
				<span class="text-xs font-medium text-slate-400">Players</span>
			</div>
			<div class="h-6 w-px shrink-0 bg-slate-700"></div>
			<div class="flex gap-2">
				{#each players.toSorted((a, b) => b.score - a.score) as player, i (player.id)}
					<div
						class="flex shrink-0 items-center gap-2 rounded-lg px-3 py-1.5 text-sm {player.hasGuessed
							? 'bg-green-500/10'
							: ''} {player.isDrawing ? 'bg-purple-500/10' : 'bg-slate-800/50'}"
					>
						<span class="text-xs font-medium text-slate-500">#{i + 1}</span>
						<div
							class="flex h-6 w-6 items-center justify-center rounded-full text-xs font-medium {player.isDrawing
								? 'bg-purple-500/20 text-purple-400'
								: player.hasGuessed
									? 'bg-green-500/20 text-green-400'
									: 'bg-slate-700 text-slate-400'}"
						>
							{player.name.charAt(0).toUpperCase()}
						</div>
						<span class="text-slate-200">{player.name}</span>
						{#if player.isDrawing}
							<Pencil class="h-3 w-3 text-purple-400" />
						{/if}
						<span class="text-xs font-medium text-purple-400">{player.score}</span>
					</div>
				{/each}
			</div>
		</div>

		<!-- Main Game Area -->
		<div class="flex min-h-0 flex-1 flex-col gap-5 md:gap-4">
			<!-- Canvas Area -->
			<div class="flex min-h-0 flex-1 flex-col gap-4">
				<!-- Canvas -->
				<div
					class="relative min-h-[320px] flex-1 overflow-hidden rounded-xl border border-slate-700 bg-slate-900 md:min-h-0"
				>
					<canvas
						class="absolute inset-0 h-full w-full cursor-crosshair bg-white"
						aria-label="Drawing canvas"
					></canvas>

					<!-- Drawing indicator -->
					<div
						class="absolute top-3 left-3 flex items-center gap-2 rounded-full bg-slate-900/80 px-3 py-1 text-xs backdrop-blur-sm"
					>
						<Pencil class="h-3 w-3 text-purple-400" />
						<span class="text-slate-300">{currentDrawer} is drawing</span>
					</div>
				</div>

				<!-- Drawing Tools (only show for drawer) -->
				{#if isDrawing}
					<div
						class="flex flex-wrap items-center justify-center gap-3 rounded-xl border border-slate-700 bg-slate-800/30 p-3"
					>
						<!-- Colors -->
						<div class="flex items-center gap-1">
							{#each colors as color}
								<button
									type="button"
									onclick={() => selectColor(color)}
									class="h-7 w-7 rounded-md border-2 transition-transform hover:scale-110"
									class:border-purple-400={selectedColor === color && !isEraser}
									class:border-slate-600={selectedColor !== color || isEraser}
									style="background-color: {color}"
									aria-label="Select color {color}"
								></button>
							{/each}
						</div>

						<div class="h-6 w-px bg-slate-700"></div>

						<!-- Brush Size -->
						<div class="flex items-center gap-2">
							<Palette class="h-4 w-4 text-slate-400" />
							<input
								type="range"
								bind:value={brushSize}
								min="2"
								max="20"
								class="w-20 accent-purple-500"
							/>
						</div>

						<div class="h-6 w-px bg-slate-700"></div>

						<!-- Tools -->
						<Button
							type="button"
							variant={isEraser ? 'default' : 'ghost'}
							size="icon-sm"
							onclick={toggleEraser}
							class={isEraser
								? 'bg-purple-600 hover:bg-purple-500'
								: 'text-slate-400 hover:text-slate-100'}
							aria-label="Eraser"
						>
							<Eraser class="h-4 w-4" />
						</Button>
						<Button
							type="button"
							variant="ghost"
							size="icon-sm"
							onclick={undoStroke}
							class="text-slate-400 hover:text-slate-100"
							aria-label="Undo"
						>
							<Undo2 class="h-4 w-4" />
						</Button>
						<Button
							type="button"
							variant="ghost"
							size="icon-sm"
							onclick={clearCanvas}
							class="text-slate-400 hover:text-red-400"
							aria-label="Clear canvas"
						>
							<Trash2 class="h-4 w-4" />
						</Button>
					</div>
				{/if}
			</div>

			<!-- Chat -->
			<div
				class="flex h-[240px] shrink-0 flex-col rounded-xl border border-slate-700 bg-slate-800/30 md:h-[280px]"
			>
				<div class="shrink-0 border-b border-slate-700/50 p-3">
					<h2 class="text-sm font-semibold text-slate-100">Chat</h2>
				</div>

				<!-- Chat Messages -->
				<div bind:this={chatContainer} class="min-h-0 flex-1 space-y-2 overflow-y-auto p-3">
					{#each chatMessages as msg (msg.id)}
						{#if msg.isSystem}
							<div class="text-center text-xs text-purple-400">
								{msg.message}
							</div>
						{:else}
							<div class="text-sm">
								<span class="font-medium text-purple-400">{msg.sender}:</span>
								<span class="text-slate-300"> {msg.message}</span>
							</div>
						{/if}
					{/each}
				</div>

				<!-- Chat Input -->
				<form onsubmit={handleSendMessage} class="shrink-0 border-t border-slate-700/50 p-3">
					<div class="flex gap-3">
						<Input
							type="text"
							bind:value={chatInput}
							placeholder={isDrawing ? "You can't chat while drawing" : 'Type your guess...'}
							disabled={isDrawing}
							maxlength={100}
							class="h-9 flex-1 border-slate-700 bg-slate-900/50 text-sm text-slate-100 placeholder:text-slate-500 focus-visible:border-purple-500 focus-visible:ring-purple-500/50"
						/>
						<Button
							type="submit"
							variant="none"
							size="icon"
							disabled={!chatInput.trim() || isDrawing}
							class="h-9 w-9 shrink-0 bg-purple-600 text-white hover:bg-purple-500 disabled:bg-slate-700 disabled:text-slate-500"
							aria-label="Send message"
						>
							<Send class="h-4 w-4" />
						</Button>
					</div>
				</form>
			</div>
		</div>
	</div>
</section>
