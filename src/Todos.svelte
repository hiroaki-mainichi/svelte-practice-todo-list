<script>
    // TodoItem component imported
    import TodoItem from './TodoItem.svelte';

    let newTodoTitle = '';
    let currentFilter = 'all';
    let nextId = 4;

    // create a dict with 3 arrays inside; array(id, title, completed )
    let todos = [
        {
            id: 1,
            title: '1st todo',
            completed: false
        },
        {
            id: 2,
            title: '2nd todo',
            completed: false
        },
        {
            id: 3,
            title: '3rd todo',
            completed: false
        },
    ];

    // detect when Enter pressed, trigger addTodo
    function addTodo(event) {
        if (event.key === 'Enter') {
            todos = [...todos, {
                id: nextId,
                completed: false,
                title: newTodoTitle
            }];
            nextId = nextId + 1;
            newTodoTitle = '';
        }
    }
    // filter out todos by completed with "false" value
    $: todosRemaining = filteredTodos.filter(todo => !todo.completed).length;
    // currentFilter all: all todos; completed: "True" status; else: "False" status
    $: filteredTodos = currentFilter === 'all' 
    ? todos : currentFilter === 'completed' 
    ? todos.filter(todo => todo.completed)
    : todos.filter(todo => !todo.completed)

    // check all items 
    function checkAllTodos(event) {
        todos.forEach(todo => todo.completed = event.target.checked);
        todos = todos;
    }

    // change filter option(currentFilter)
    function updateFilter(newFilter) {
        currentFilter = newFilter;
    }

    // filter items with status of "True"(inverse of default False)
    function clearCompleted() {
        todos = todos.filter(todo => !todo.completed);
    }

    // delete process: delete a specific todo element
    function handleDeleteTodo(event) {
        todos = todos.filter(todo => todo.id !== event.detail.id);
    }

    // change "completed" status
    function handleToggleComplete(event) {
        // retrieve the specified todo ID
        const todoIndex = todos.findIndex(todo => todo.id === event.detail.id);
        // update the "completed" status of the ID
        const updatedTodo = { ...todos[todoIndex], completed: !todos[todoIndex].completed};
        // "...todos": new todo element created with "completed" value changed
        todos = [
            ...todos.slice(0, todoIndex),
            updatedTodo,
            ...todos.slice(todoIndex + 1),
        ];
    }

</script>

<style>
    .container {
        max-width: 800px;
        margin: 10px auto;
    }
    .logo {
        display: block;
        margin: 20px auto;
        width: 50%;
    }
    .todo-input {
        width: 100%;
        padding: 10px, 20px;
        font-size: 18px;
        margin-bottom: 20px;
    }
    .inner-container {
        display: flex;
        align-items: center;
        justify-content: space-between;
        font-size: 16px;
        border-top: 1px solid lightgrey;
        padding-top: 15px;
        margin-bottom: 13px;
    }
    .inner-container-input {
        margin-right: 12px;
    }
    button {
        font-size: 14px;
        background-color: white;
        appearance: none;
    }
    button:hover {
        background: lightseagreen;
    }
    button:focus {
        outline: none;
    }
    .active {
        background: lightseagreen;
    }
</style>

<div class="container">
    <a href="https://codingthesmartway.com" target="_blank"><img src={'/img/CTSWLogo.png'} alt="svelte logo" class="logo"></a>
    <h2>Svelte Todo App</h2>
    <input type="text" class="todo-input" placeholder="Insert todo item ..." bind:value={newTodoTitle} on:keydown={addTodo}>
    <!-- iterate through filetered todo list -->
    {#each filteredTodos as todo}
        <div class="todo-item">
        <!-- {...todo} component property passed-->
            <TodoItem {...todo} on:deleteTodo={handleDeleteTodo} on:toggleComplete={handleToggleComplete} />
        </div>
    {/each}
    <div class="inner-container">
        <div><label><input class="inner-container-input" type="checkbox" on:change={checkAllTodos}>Check All</label></div>
        <div>{todosRemaining} items left</div>
    </div>
    <div class="inner-container">
        <div>
            <button on:click={() => updateFilter('all')} class:active="{currentFilter === 'all'}">All</button>
            <button on:click={() => updateFilter('active')} class:active="{currentFilter === 'active'}">Active</button>
            <button on:click={() => updateFilter('completed')} class:active="{currentFilter === 'completed'}">Completed</button>
        </div>
        <div>
            <button on:click={clearCompleted}>Clear Completed</button>
        </div>
    </div>
</div>