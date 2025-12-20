<script lang="ts">
	import { PaintbrushVertical, User, Play, CirclePlus, Hash } from '@lucide/svelte';

	let username = $state('');

	function handleSubmit() {
		console.log('Hello World!');
	}
</script>

<svelte:head>
	<title>Ink - Draw & Guess Together</title>
</svelte:head>

<section class="flex min-h-svh items-center justify-center" aria-label="Welcome">
	<div class="flex flex-col items-center gap-6">
		<header class="flex items-center gap-2">
			<PaintbrushVertical class="h-12 w-12 text-purple-400" aria-hidden="true" />
			<h1 class="text-5xl font-bold">Ink</h1>
		</header>

		<form class="w-full max-w-sm" onsubmit={handleSubmit}>
			<label for="username" class="mb-2 block text-sm font-medium text-slate-300">
				Your Name
			</label>
			<div class="relative">
				<div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3">
					<User class="h-5 w-5 text-slate-400" aria-hidden="true" />
				</div>
				<input
					type="text"
					id="username"
					name="username"
					bind:value={username}
					placeholder="Enter your name"
					maxlength="20"
					autocomplete="username"
					required
					aria-required="true"
					aria-describedby={username.length > 0 ? 'username-count' : undefined}
					class="w-full rounded-lg border border-slate-700 bg-slate-800/50 py-3 pr-4 pl-10 text-slate-100 placeholder-slate-500 transition-colors focus:border-purple-500 focus:ring-2 focus:ring-purple-500/50 focus:outline-none"
				/>
			</div>
			{#if username.length > 0}
				<p id="username-count" class="mt-1 text-xs text-slate-400" aria-live="polite">
					{username.length}/20 characters
				</p>
			{/if}
		</form>

		<nav class="flex w-full max-w-sm flex-col gap-3" aria-label="Game options">
			<button
				type="button"
				disabled={username.length === 0}
				class="flex items-center justify-center gap-2 rounded-lg bg-purple-600 px-6 py-3.5 font-semibold text-white shadow-lg shadow-purple-900/50 transition-all hover:bg-purple-500 hover:shadow-xl hover:shadow-purple-900/60 focus:ring-2 focus:ring-purple-500 focus:ring-offset-2 focus:ring-offset-slate-950 focus:outline-none disabled:cursor-not-allowed disabled:opacity-50 disabled:hover:bg-purple-600 disabled:hover:shadow-lg"
			>
				<Play class="h-5 w-5" aria-hidden="true" />
				Play Now
			</button>
			<button
				type="button"
				disabled={username.length === 0}
				class="flex items-center justify-center gap-2 rounded-lg border border-slate-600 bg-slate-800/50 px-6 py-3 font-medium text-slate-100 transition-all hover:border-slate-500 hover:bg-slate-700/50 focus:ring-2 focus:ring-purple-500 focus:ring-offset-2 focus:ring-offset-slate-950 focus:outline-none disabled:cursor-not-allowed disabled:opacity-50 disabled:hover:border-slate-600 disabled:hover:bg-slate-800/50"
			>
				<CirclePlus class="h-5 w-5" aria-hidden="true" />
				Create Lobby
			</button>
			<button
				type="button"
				disabled={username.length === 0}
				class="flex items-center justify-center gap-2 rounded-lg border border-slate-600 bg-slate-800/50 px-6 py-3 font-medium text-slate-100 transition-all hover:border-slate-500 hover:bg-slate-700/50 focus:ring-2 focus:ring-purple-500 focus:ring-offset-2 focus:ring-offset-slate-950 focus:outline-none disabled:cursor-not-allowed disabled:opacity-50 disabled:hover:border-slate-600 disabled:hover:bg-slate-800/50"
			>
				<Hash class="h-5 w-5" aria-hidden="true" />
				Join with Code
			</button>
		</nav>
	</div>
</section>
