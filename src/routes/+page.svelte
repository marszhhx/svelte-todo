<script>
    let todos = $state([])

    const Filters = {
    ALL: 'all',
    ACTIVE: 'active',
    COMPLETED: 'completed',
    };

    let filter = $state(Filters.ALL)
    let filteredTodos = $derived(filterTodos())

    function addTodo(e) {
        if (e.key !== 'Enter') return
        const todoElement = e.target
        const text = todoElement.value
        const done = false
        todos = [...todos, {text, done}]
        todoElement.value = ''
    }

    function editTodo(e) {
        const inputElement = e.target
        const index = inputElement.dataset.index
        todos[index].text = inputElement.value
    }

    function toggleTodo(e) {
        const inputElement = e.target
        const index = inputElement.dataset.index
        todos[index].done = !todos[index].done
        todos = [...todos]
    }

    function setFilter(newFilter) {
        filter = newFilter
    }

    function filterTodos() {
        switch (filter) {
            case 'all':
                return todos
            case 'active':
                return todos.filter(todo => !todo.done)
            case 'completed':
                return todos.filter(todo => todo.done)
        }
    }

    function remaining() {
        return todos.filter(todo => !todo.done).length
    }

    $effect(() => {
        const savedTodos = localStorage.getItem('todos')
        if (savedTodos) {
            todos = JSON.parse(savedTodos)
        }
        // console.log(todos)
        // console.log(filter)
        // console.log(filteredTodos)
    })

    $effect(() => {
        localStorage.setItem('todos', JSON.stringify(todos))
    })


</script>

<div class='todos'>
    <input type='text' placeholder="Add todo" onkeydown={addTodo}>
    {#each filteredTodos as todo, i }
    <div class='todo' class:completed={todo.done}>
        <input oninput={editTodo} data-index={i} type='text' value={todo.text}>
        <input onchange={toggleTodo} data-index={i} type='checkbox' checked={todo.done}>
    </div>
    {/each}
</div>

<div class='filters'>
    {#each [Filters.ALL, Filters.ACTIVE, Filters.COMPLETED] as currentFilter}
    <button onclick={() => setFilter(currentFilter)} class={filter === currentFilter ? 'active-filter' : ''}>{currentFilter}</button>
    {/each}
</div>

<p>{remaining()} remaining</p>

<style>
body, html {
  height: 100%;
  margin: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  font-family: Arial, sans-serif;
}

.todos {
  display: flex;
  flex-direction: column;
  width: 30%;
  max-width: 500px;
  margin: auto;
  box-shadow: 0 4px 8px rgba(0,0,0,0.1);
}

.todo {
  display: flex;
  align-items: center;
  border-bottom: 1px solid #efefef;
  padding: 10px;
}

.todo:last-child {
  border-bottom: none;
}

.todo input[type='text'] {
  flex: 1;
  padding: 10px;
  border: 1px solid #ddd;
  margin-right: 10px;
  outline: none;
}

.todo input[type='checkbox'] {
  transform: scale(1.2);
}

.todo.completed {
  background-color: #f4f4f4;
  text-decoration: line-through;
}

.filters {
  display: flex;
  justify-content: center;
  padding: 20px;
}

.filters button {
  border: 1px solid #ddd;
  padding: 10px 20px;
  cursor: pointer;
  margin: 0 5px;
  outline: none;
  transition: background-color 0.3s ease;
}

.active-filter {
  background-color: #007bff; /* A bright blue, for example */
  border-color: #007bff; /* Same as background for consistency */
}

p {
  text-align: center;
  color: #666;
}

/* Add some padding at the bottom for better spacing */
body {
  padding-bottom: 50px;
}

</style>