<script lang="ts">
	import "../app.css";
	import { Button } from "$lib/components/ui/button";

	import { invalidate } from '$app/navigation';
	import { onMount } from 'svelte';

	export let data;
	$: ({ session, supabase } = data);

	onMount(() => {
		const { data } = supabase.auth.onAuthStateChange((_, newSession) => {
			if (newSession?.expires_at !== session?.expires_at) {
				invalidate('supabase:auth');
			}
		});

		return () => data.subscription.unsubscribe();
	});
</script>

<nav class="bg-gray-800 p-4">
  <div class="container mx-auto flex items-center justify-between">
    <a href="/" class="text-white text-2xl font-bold">Logu</a>
    <div class="flex items-center space-x-4">
      <Button variant="ghost">
        <a href="/tasks" class="text-white hover:text-gray-300">タスク</a>
      </Button>
	  {#if session}
	  <Button variant="ghost">
        <a href="/tasks" class="text-white hover:text-gray-300">{session.user.email}</a>
      </Button>
	  {:else}
	  <Button variant="ghost">
        <a href="/auth" class="text-white hover:text-gray-300">ログイン</a>
      </Button>
	  {/if}
    </div>
  </div>
</nav>

<main class="container mx-auto mt-8">
  <slot />
</main>
