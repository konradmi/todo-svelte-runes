<script lang='ts'>
  type Filter = 'all' | 'active' | 'completed'

  type Todo = {
    text: string
    done: boolean
    id: number
  }

  let todos = $state([
    { text: 'TODO 1', done: false, id: crypto.getRandomValues(new Uint32Array(1))[0] },
    { text: 'TODO 2', done: true, id: crypto.getRandomValues(new Uint32Array(1))[0] },
    { text: 'TODO 3', done: false, id: crypto.getRandomValues(new Uint32Array(1))[0] },
  ])

  let filter = $state<Filter>('all')

  const addTodo = (e: KeyboardEvent) => {
    if (e.key === 'Enter') {
      const target  = e.target as HTMLInputElement
      todos = [...todos, { text: target.value, done: false, id: crypto.getRandomValues(new Uint32Array(1))[0] }]

      target.value = ''
    }
  }
  const editTodo = (e: Event, id: number) => {
    const todo = todos.find(todo => todo.id === id)!
    todo.text = (e.target as HTMLInputElement).value
  }

  const onChange = (e: Event, id: number) => {
    const todo = todos.find(todo => todo.id === id)!
    todo.done = !todo.done
  }

  const filterTodos = (todos: Todo[], filter: Filter) => {
    switch (filter) {
      case 'active': 
        return todos.filter(todo => todo.done === false)
      case 'completed':
        return todos.filter(todo => todo.done === true)
      default:
        return todos
    }
  }

  const calculateNumberOfTodos = (todos: Todo[]) => {
    console.log('calculating')
    return todos.length
  }

  let filteredTodos = $derived(filterTodos(todos, filter))
</script>

<div class='page'>
  <div>{`Number of todos: ${calculateNumberOfTodos(todos)}`}</div>
  <div class='filters'>
    <button onclick={() => filter = 'all'}>All</button>
    <button onclick={() => filter = 'active'}>Active</button>
    <button onclick={() => filter = 'completed'}>Completed</button>
  </div>
  <input class='inputTodo' onkeydown={addTodo} type='text' placeholder="Add todo">
  <div class='todos'>
    {#each filteredTodos as todo (todo.id)}
      <div class='todo'>
        <input onchange={e => onChange(e, todo.id)} type='checkbox' checked={todo.done}/>
        <input oninput={e => editTodo(e, todo.id)} type='text' value={todo.text}/>
      </div>
    {/each}
  </div>
</div>

<style>
  .filters {
    margin-bottom: 1rem;
  }

  .inputTodo {
    align-self: flex-end;
    margin-bottom: 1rem;
  }

  .page {
    display: flex;
    flex-direction: column;
    align-items: center;
  }
  .todos {
    display: flex;
    flex-direction: column;
    margin-bottom: 5rem;
  }

  .todo {
    display: flex;
    align-items: center;
  }

  .todo input[type='checkbox'] {
    margin-right: 1rem;
  }
</style>
