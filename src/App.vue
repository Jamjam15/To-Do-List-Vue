<script setup>
import { ref, onMounted, computed, watch } from "vue";

const todos = ref([]);
const name = ref("");

const input_content = ref('');
const input_category = ref(null)

// To Dos sorted from newest to oldest
const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

// Store To Do
watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {deep: true})

// Store Name
watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

// Load Name and To Do List on Load Page
onMounted(() => {
  name.value = localStorage.getItem('name') || ''

  const savedTodos = localStorage.getItem('todos')
  if(savedTodos) {
    todos.value = JSON.parse(savedTodos)
  }
})


// Function to Add To Do List
const addToDo = () => {
  // If the input is empty or user hasn't picked a category
  if (input_content.value.trim() === '' || input_category.value === null)
  {
    // Stops the function
    return 
  }

  // If the input is valid, it adds a new object to the to do list
  todos.value.push({
    content: input_content.value,    //content: "Buy Groceries",
    category: input_category.value,  //category: "personal",
    done: false,                     //done: false,
    createdAt: new Date().getTime()  //createdAt: 1729312838123 
  })
}


const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo)
}

</script>

<template>

  <main class="app">

    <section class="greeting">
      <h2 class="title">
        what's up, <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form @submit.prevent="addToDo">
        <h4>What's on your to do list?</h4>
        <input 
          type="text" 
          placeholder="e.g. make a video" 
          v-model="input_content" />

          <h4>Pick a category</h4>

          <div class="options">

            <label>
              <input 
                type="radio" 
                name="category"
                id="category1"
                value="business"
                v-model="input_category" />
                <span class="bubble business"></span>
                <div>Business</div>
            </label>

            <label>
              <input 
                type="radio" 
                name="category"
                id="category1"
                value="personal"
                v-model="input_category" />
                <span class="bubble personal"></span>
                <div>Personal</div>
            </label>

          </div>

          <input type="submit" value="Add ToDo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>To Do List</h3>
      <div class="list" id="todo-list">
        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">

          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category == 'business' ? 'business' : 'personal'}`"></span>
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