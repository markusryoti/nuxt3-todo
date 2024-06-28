<template>
  <div class="container mx-auto">
    <h1 class="text-5xl">Todos</h1>
    <div class="py-4">
      <input v-model="inputValue" type="text" placeholder="add todo" v-on:keydown="addTodo"
        class="border rounded-xl p-2 w-96">
    </div>
    <ol class="py-4 list-decimal list-inside">
      <li v-for="todo in todos" class="py-2">
        <span v-on:click="() => todo.done = !todo.done" v-bind:class="{ 'line-through': todo.done }"
          class="pl-2 pr-4">{{
            todo.name }}</span>
        <button v-on:click="() => removeTodo(todo.id)"
          class="bg-red-400 text-red-800 w-8 h-8 rounded rounded-full">X</button>
      </li>
    </ol>
  </div>
</template>

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
  localStorage.setItem('todos', JSON.stringify(todos.value))
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
  todos.value = todos.value.filter((t) => t.id !== id)
}

</script>
