<script lang="ts">
	import { PaintbrushVertical } from '@lucide/svelte';
</script>

<div class="flex min-h-svh items-center justify-center">
	<div class="flex flex-col items-center gap-6">
		<div class="flex items-center gap-3">
			<PaintbrushVertical class="h-12 w-12 text-purple-400" />
			<h1 class="text-5xl font-bold">Ink</h1>
		</div>

		<input type="text" placeholder="Enter your name" />
		<button>Play Now</button>
		<button>Create Lobby</button>
		<button>Join with Code</button>
	</div>
</div>
