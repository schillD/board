<script lang="ts">
  import { currentUser, pb } from "../pocketbase.js";
  import "bootstrap/dist/css/bootstrap.min.css";

  import { Button, Input } from "sveltestrap";

  let username: string;
  let password: string;

  async function login() {
    await pb.collection("users").authWithPassword(username, password);
  }

  async function signUp() {
    try {
      const data = {
        username,
        password,
        passwordConfirm: password,
        name: username,
      };
      const createdUser = await pb.collection("users").create(data);
      await login();
    } catch (err) {
      console.error(err);
    }
  }

  function signOut() {
    pb.authStore.clear();
  }
</script>

<div class="container">
  {#if $currentUser}
    <h3>Sigend In</h3>
  {:else}
    <form on:submit|preventDefault>
      <Input placeholder="Username" type="text" bind:value={username} />

      <Input placeholder="Password" type="password" bind:value={password} />
      <Button on:click={signUp}>Sign Up</Button>
      <Button on:click={login}>Login</Button>
    </form>
  {/if}
</div>

<style>
  .container {
    max-width: 400px;
    margin: 0 auto;
    padding: 2rem;
    text-align: center;
  }
</style>
