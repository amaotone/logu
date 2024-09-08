<script lang="ts">
import { Button } from "$lib/components/ui/button";
export let data;
$: ({ countries } = data);
</script>

<h1 class="text-3xl font-bold mb-4">Loguへようこそ</h1>
<p class="mb-4">効率的なタスク管理ツールです。</p>
<Button href="/tasks">タスクを管理する</Button>
<ul>
    {#each countries as country}
        <li>{country.name}</li>
    {/each}
</ul>
