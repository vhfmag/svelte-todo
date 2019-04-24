<script>
    import { flip } from 'svelte/animate';
    import { fade } from 'svelte/transition';
    
    export let todos = [];
    export let fallback = "";
    export let receive = fade;
    export let send = fade;
    export let removeTodo;
    export let editTodo;
</script>

<style>
	.todo-list {
		margin: 0;
		padding: 0;
	}

	.todo-list li {
		list-style: none;
    }
    
	.todo-list li + li {
        margin-top: 0.5em;
    }

	.todo-list li label {
		display: grid;
		align-items: center;
		grid-gap: 0.5em;
		grid-template-columns: min-content auto min-content;
	}
</style>

<ul class="todo-list">
    {#each todos as todo (todo.id)}
        <li
            in:receive="{{key: todo.id}}"
            out:send="{{key: todo.id}}"
            animate:flip={{ duration: 200 }}
        >
            <label>
                <input
                    type='checkbox'
                    checked={todo.done}
                    on:change={() => editTodo({ ...todo, done: !todo.done })}
                />
                {todo.text}
                <button on:click={() => removeTodo(todo.id)}>Remover</button>
            </label>
        </li>
    {/each}
</ul>

{#if todos.length === 0 && fallback}
<div in:receive out:send>{fallback}</div>
{/if}