<script lang="ts">
import { onMount } from 'svelte';
import { invalidate } from '$app/navigation';
import { Button } from "$lib/components/ui/button";
import { supabase } from '$lib/supabase';
import type { User } from '@supabase/supabase-js';

let user: User | null = null;

onMount(() => {
    const fetchUser = async () => {
        const { data: { user: initialUser }, error } = await supabase.auth.getUser();
        if (error) {
            console.error('Error fetching user:', error);
        } else {
            user = initialUser;
        }
    }

    fetchUser();

    const { data: authListener } = supabase.auth.onAuthStateChange(async (event, _session) => {
        if (event === 'SIGNED_IN' || event === 'TOKEN_REFRESHED') {
            const { data: { user: newUser }, error } = await supabase.auth.getUser();
            if (error) {
                console.error('Error fetching user:', error);
            } else {
                user = newUser;
            }
        } else if (event === 'SIGNED_OUT') {
            user = null;
        }
        invalidate('supabase:auth');
    });

    return () => {
        authListener.subscription.unsubscribe();
    };
});

async function handleSignOut() {
    const { error } = await supabase.auth.signOut();
    if (error) console.error('Error signing out:', error);
    user = null;
    invalidate('supabase:auth');
}
</script>

{#if user}
    <span class="text-white">{user.email}</span>
    <Button variant="ghost" on:click={handleSignOut}>ログアウト</Button>
{:else}
    <Button variant="ghost">
        <a href="/auth" class="text-white hover:text-gray-300">ログイン</a>
    </Button>
{/if}
