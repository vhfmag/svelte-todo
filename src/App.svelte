<script>
	import { quintOut } from 'svelte/easing';
	import { crossfade } from 'svelte/transition';
	import { flip } from 'svelte/animate';
	import TaskList from './TaskList.svelte';

	const sampleTodos = [
		{
			id: Math.random(),
			text: "Fazer café",
			done: true,
		},
		{
			id: Math.random(),
			text: "Tomar remédio",
			done: true,
		},
		{
			id: Math.random(),
			text: "Comprar pão",
			done: false,
		}
	];

	export let todos = sampleTodos;
	let description = "";

	const [send, receive] = crossfade({
		duration: d => Math.sqrt(d * 200),
		fallback(node, params) {
			const style = getComputedStyle(node);
			const transform = style.transform === 'none' ? '' : style.transform;

			return {
				duration: 600,
				easing: quintOut,
				css: t => `
					transform: ${transform} scale(${t});
					opacity: ${t}
				`
			};
		},
	});

	function removeTodo(id) {
		todos = todos.filter(todo => todo.id !== id);
	}

	function editTodo(editedTodo) {
		todos = todos.map(todo => todo.id === editedTodo.id ? editedTodo : todo);
	}

	function addTodo({ text, done = false, id = Math.random() }) {
		todos = [...todos, { text, done, id }];
	}

	function onSubmitNewTodo() {
		addTodo({ text: description });
		description = "";
	}
</script>

<style>
	h1, h2, h3, h4, h5, h6 {
		color: palevioletred;
	}

	.lists {
		display: grid;
		grid-gap: 1em;
		grid-template-columns: repeat(2, minmax(200px, max-content));
	}

	.new-todo-form {
		display: grid;
		grid-gap: 0.5em 1em;
		grid-template-columns: repeat(2, max-content);
		grid-template-areas: "label label" "input button";
		margin-bottom: 0.5em;
	}

	.new-todo-form label {
		grid-area: label;
	}
</style>

<h1>Afazeres</h1>

<form class="new-todo-form" on:submit|preventDefault={onSubmitNewTodo}>
	<label for="new-todo-input">O que precisa ser feito?</label>
	<input id="new-todo-input" bind:value={description}>
	<button>Adicionar</button>
</form>

<div class="lists">
	<section>
		<h2>A fazer</h2>
		<TaskList
			fallback="Nada a fazer :)"
			todos={todos.filter(t => !t.done)}
			receive={receive}
			send={send}
			removeTodo={removeTodo}
			editTodo={editTodo}
		/>
	</section>
	
	<section>
		<h2>Feito</h2>
		<TaskList
			fallback="It's time to work!"
			todos={todos.filter(t => t.done)}
			receive={receive}
			send={send}
			removeTodo={removeTodo}
			editTodo={editTodo}
		/>
	</section>
</div>
