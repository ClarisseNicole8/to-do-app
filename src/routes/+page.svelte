<svelte:head>
    <link rel="stylesheet" href="https://unpkg.com/@picocss/pico@latest/css/pico.min.css">
</svelte:head>

<script>
    let toDoList = [];
    let textInput = "";

    function addTodo() {
        toDoList = [...toDoList, { content: textInput, editing: false, checked: false }]
    }

    function setEditing(i, isEditing) {
        toDoList[i].editing = isEditing;
    }

    function deleteTodo(i) {
        toDoList.splice(i, 1);
        toDoList = toDoList;
    }

</script>

<!-- todos.svelte -->
<script>
    import { onMount } from 'svelte';
    import { getFirestore, collection, addDoc, onSnapshot, updateDoc, deleteDoc, doc } from 'firebase/firestore';
    import { writable } from 'svelte/store';

    const db = getFirestore(app);
    let toDoList = writable([]);

    onMount(() => {
        const todosCollection = collection(db, 'todos');

        // Fetch todos from Firebase on mount
        const unsubscribe = onSnapshot(todosCollection, (snapshot) => {
            toDoList.set(snapshot.docs.map(doc => ({ id: doc.id, ...doc.data() })));
        });

        // Cleanup the subscription when component is destroyed
        return () => unsubscribe();
    });

    function addTodo() {
        if (textInput.trim() !== '') {
            const todosCollection = collection(db, 'todos');
            addDoc(todosCollection, { content: textInput, editing: false, checked: false });
        }
    }

    function setEditing(id, isEditing) {
        const todoDoc = doc(db, 'todos', id);
        updateDoc(todoDoc, { editing: isEditing });
    }

    function deleteTodo(id) {
        const todoDoc = doc(db, 'todos', id);
        deleteDoc(todoDoc);
    }
</script>

<div style="margin: 0 auto; padding: 20px; width: 700px;">
    <h2 style="text-align: center;">To Do List</h2>
    <p>Enter your ToDo here</p>
    <div style="display: flex;">
        <input type="text" bind:value={textInput}>
        <button style="width: 200px" on:click={addTodo}>Add</button>
    </div>
</div>

{#each toDoList as toDo, i}
    <div style="display: flex; align-items: baseline; width: 700px; margin: 0 auto;">
        {#if toDo.editing }
            <input type="text" bind:value={toDo.content}>
        {:else}
            <input type="checkbox" bind:checked = {toDo.checked}>
            <h4 style="flex-grow: 1">{toDo.content}</h4>
        {/if}
        <div style="display: flex">
            {#if toDo.editing }
                <button on:click={() => setEditing(i, false)}>Save</button>
            {:else}
                <button on:click={() => setEditing(i, true)}>Edit</button>
            {/if}
                <button on:click={() => deleteTodo(i)}>Delete</button>
        </div>
    </div>
{/each}
