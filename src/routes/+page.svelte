<script lang="ts">
	import { PaintbrushVertical, User } from '@lucide/svelte';

	let username = $state('');
</script>

<div class="flex min-h-svh items-center justify-center">
	<div class="flex flex-col items-center gap-6">
		<div class="flex items-center gap-3">
			<PaintbrushVertical class="h-12 w-12 text-purple-400" />
			<h1 class="text-5xl font-bold">Ink</h1>
		</div>

		<div class="w-full max-w-sm">
			<label for="username" class="mb-2 block text-sm font-medium text-slate-300">
				Your Name
			</label>
			<div class="relative">
				<div class="pointer-events-none absolute inset-y-0 left-0 flex items-center pl-3">
					<User class="h-5 w-5 text-slate-400" />
				</div>
				<input
					type="text"
					id="username"
					bind:value={username}
					placeholder="Enter your name"
					maxlength="20"
					autocomplete="username"
					class="w-full rounded-lg border border-slate-700 bg-slate-800/50 py-3 pr-4 pl-10 text-slate-100 placeholder-slate-500 transition-colors focus:border-purple-500 focus:ring-2 focus:ring-purple-500/50 focus:outline-none"
				/>
			</div>
			{#if username.length > 0}
				<p class="mt-1 text-xs text-slate-400">{username.length}/20 characters</p>
			{/if}
		</div>

		<button>Play Now</button>
		<button>Create Lobby</button>
		<button>Join with Code</button>
	</div>
</div>
