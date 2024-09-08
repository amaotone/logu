<script lang="ts">
import { onMount } from 'svelte';
import { Button } from "$lib/components/ui/button";
import { supabase } from '$lib/supabase';
import type { Task } from '$lib/types';

let tasks: Task[] = [];

onMount(async () => {
  const { data, error } = await supabase
    .from('tasks')
    .select('*')
    .order('created_at', { ascending: false });

  if (error) {
    console.error('Error fetching tasks:', error);
  } else {
    tasks = data;
  }
});

async function addTask() {
  const { data, error } = await supabase
    .from('tasks')
    .insert([
      { title: '新しいタスク', description: '説明を入力してください', is_completed: false }
    ])
    .select();

  if (error) {
    console.error('Error adding task:', error);
  } else if (data) {
    tasks = [data[0], ...tasks];
  }
}
</script>

<h1 class="text-3xl font-bold mb-4">タスク一覧</h1>

<Button on:click={addTask} class="mb-4">新しいタスクを追加</Button>

{#if tasks.length > 0}
  <ul>
    {#each tasks as task (task.id)}
      <li class="mb-2">
        <h3 class="text-xl font-semibold">{task.title}</h3>
        <p>{task.description}</p>
      </li>
    {/each}
  </ul>
{:else}
  <p>タスクがありません。新しいタスクを追加してください。</p>
{/if}
