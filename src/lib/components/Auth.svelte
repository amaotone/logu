<script lang="ts">
import { supabase } from '$lib/supabase';
import { Button } from '$lib/components/ui/button';
import { Input } from '$lib/components/ui/input';
import { goto } from '$app/navigation';

let email = '';
let password = '';
let loading = false;
let error: string | null = null;

async function handleLogin() {
  try {
    loading = true;
    error = null;
    const { error: signInError, data } = await supabase.auth.signInWithPassword({ email, password });
    if (signInError) throw signInError;
    if (data.user) {
      goto('/tasks'); // ログイン成功後にタスクページにリダイレクト
    }
  } catch (e) {
    if (e instanceof Error) {
      error = e.message;
    }
  } finally {
    loading = false;
  }
}

async function handleSignUp() {
  try {
    loading = true;
    error = null;
    const { error: signUpError, data } = await supabase.auth.signUp({ email, password });
    if (signUpError) throw signUpError;
    if (data.user) {
      alert('確認メールを送信しました。メールを確認してアカウントを有効化してください。');
      goto('/'); // サインアップ成功後にホームページにリダイレクト
    }
  } catch (e) {
    if (e instanceof Error) {
      error = e.message;
    }
  } finally {
    loading = false;
  }
}
</script>

<form on:submit|preventDefault>
  <div class="space-y-4">
    <Input type="email" placeholder="メールアドレス" bind:value={email} />
    <Input type="password" placeholder="パスワード" bind:value={password} />
    {#if error}
      <p class="text-red-500">{error}</p>
    {/if}
    <div class="flex space-x-2">
      <Button on:click={handleLogin} disabled={loading}>ログイン</Button>
      <Button on:click={handleSignUp} disabled={loading} variant="outline">新規登録</Button>
    </div>
  </div>
</form>
