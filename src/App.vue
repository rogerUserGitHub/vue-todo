<script setup lang="ts">
  import { ref, onMounted, computed, watch} from 'vue'

  interface todo {
    content: string,
    category: any,
    done: boolean,
    createdAt: any,
  }

  const todos = ref<todo[]>([])
  const name = ref('')

  const input_content = ref<string>('')
  const input_category = ref<any>(null)

  const todos_asc = computed(() => todos.value.sort((a: any, b: any) => {
    return b.createdAt - a.createdAt
  }))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: Date.now(),
  })
  input_content.value = ''
  input_category.value = null
}

const removeTodo = (todo: todo) => {
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, (newVal: any) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {deep: true})

watch(name, (newVal: string) => {
  localStorage.setItem('name', newVal)
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos') || "")
})
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        Hello, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>Create a ToDo</h3>
      <form @submit.prevent="addTodo">
        <h4>
          What's on your todo list?
        </h4>
          <input type="text" placeholder="e.g. make a video" v-model="input_content" />
            {{ input_content}}
        <h4>Pick a category</h4>
        <div class="options">

          <label>
            <input type="radio" name="category" value="business" v-model="input_category" />
            <span class="bubble business">  </span>
            <div>business</div>
          </label>

          <label>
            <input type="radio" name="category" value="personal" v-model="input_category" />
            <span class="bubble business">  </span>
            <div>Personal</div>
          </label>

          

        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>
        Todo list
      </h3>
      <div class="list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`"> 
          <label>
            <input type="checkbox" v-model="todo.done"/>
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>

  </main>  


</template>

