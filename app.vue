<script setup lang="ts">

type Todo = {
  id: string
  name: string
  done: boolean
}

const todos = ref<Todo[]>([])
const inputValue = ref("")
const isMounted = ref(false)

onMounted(() => {
  if (import.meta.client) {
    const stored = localStorage.getItem('todos')
    if (stored) {
      todos.value = JSON.parse(stored)
    }
  }

  isMounted.value = true
})

watchEffect(() => {
  if (import.meta.server) return
  if (!isMounted.value) return
  const encoded = JSON.stringify(todos.value)
  localStorage.setItem('todos', encoded)
})

function addTodo(e: any) {
  if (e.key !== "Enter") return

  const newTodo = {
    id: window.crypto.randomUUID(),
    name: inputValue.value,
    done: false
  }

  todos.value.push(newTodo)
  inputValue.value = ""
}

function removeTodo(id: string) {
  const filtered = todos.value.filter((t) => t.id !== id)
  todos.value = filtered
}

function toggleDone(id: string) {
  const edited = todos.value.map((t) => {
    if (t.id === id) t.done = !t.done
    return t
  })

  todos.value = edited
}
</script>

<template>
  <div class="container mx-auto">
    <h1>Todos</h1>
    <div>
      <input v-model="inputValue" type="text" placeholder="add todo" v-on:keydown="addTodo">
    </div>
    <ol>
      <li v-for="todo in todos" class="flex gap-4">
        <span v-on:click="() => toggleDone(todo.id)" v-bind:class="{ 'line-through': todo.done }">{{ todo.name }}</span>
        <button v-on:click="() => removeTodo(todo.id)">X</button>
      </li>
    </ol>
  </div>
</template>
