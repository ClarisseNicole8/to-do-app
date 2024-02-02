<!-- src/routes/todos.svelte -->
<script>
    let todos = [];
    let newTodo = '';

    function addTodo() {
        if (newTodo.trim() !== '') {
            todos = [...todos, { id: Date.now(), text: newTodo, completed: false}];
            newTodo = '';
        }
    }

    function removeTodo(id) {
        todos = todos.filter(todo => todo.id !==id);
    }

    function toggleTodo(id) {
        todos = todos.map(todo => (todo.id === id ? { ...todo, completed: !todo.completed } : todo));
    }

</script>

<main>
    <h1>Todo App</h1>
    <input bind:value={newTodo} placeholder="Add a new todo" />
    <button on:click={addTodo}>Add Todo</button>

    <ul>
        {#each todos as {id, text, completed }}
        <li>
            <input type="checkbox" bind:checked={completed} />
            <span>{text}</span>
            <button on:click={() => removeTodo(id)}>Remove</button>
        </li>
        {/each}
    </ul>
</main>

<style>
    main {
      max-width: 400px;
      margin: 0 auto;
    }

    ul {
      list-style: none;
      padding: 0;
    }

    li {
      display: flex;
      align-items: center;
      margin-bottom: 8px;
    }

    input[type="checkbox"] {
      margin-right: 8px;
    }
  </style>
