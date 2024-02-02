<!-- src/routes/index.svelte -->
<script>
    import { goto } from '$app/navigation';
  </script>

  <main>
    <h1>Welcome to SvelteKit!</h1>
    <p>Click the button below to go to the Todo App:</p>
    <button on:click={() => goto('/todos')}>Go to Todo App</button>
  </main>
