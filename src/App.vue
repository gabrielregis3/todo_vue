<script setup>
  import { ref, onMounted, computed, watch } from 'vue'

  const todos = ref([])
  const name = ref('')
  const input_content = ref('')
  const input_category = ref(null)

  const todos_asc = computed(() => todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt
  }))

  const addToDo = () => {

    if (input_content.value.trim() === '' || input_category.value === null) {
      return
    }

    todos.value.push({
      content: input_content.value,
      category: input_category.value,
      done: false,
      createdAt: new Date().getTime()
    })

    //Reset the tasks
    input_content.value = ''
    input_category.value = null
  }

  const removeTodo = todo => {
    todos.value = todos.value.filter(t => t !== todo)
  }

  const clearAllTasks = () => {

  if (!todos) {
    return;
  }

  const userConfirmed = window.confirm("You want to delete all the tasks?")

  if (userConfirmed) {
    todos.value = []
  }
  }

  //watch just do something when the value changes
  watch(todos, (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal))
  }, { deep: true }) //Look if anything changes on the todos deep catches and calls the function

  watch(name, (newVal) => {
    localStorage.setItem('name', newVal)
  })

  onMounted(() => {
    name.value = localStorage.getItem('name') || ''
    todos.value = JSON.parse(localStorage.getItem('todos')) || []
  })
</script>

<template>
  <main class="app">
      <section class="greeting">
        <h2 class="title">
          What's up, <input type="text" placeholder="Enter name" v-model="name" /> <!--v-model(Any change made to the input (when the user types something) will automatically be reflected on the variable inside of it) -->
        </h2>
      </section>

      <section class="create-todo">

        <form @submit.prevent="addToDo"> <!--At submit prevent default -->
          <h4>What's on your todo list?</h4>
          <input type="text" placeholder="e.g. read a book" v-model="input_content" />

          <h4>Pick a category</h4>

          <div class="options">
            <label>
              <input type="radio" name="category" value="work" v-model="input_category"/>
              <span class="bubble work"></span>
              <div>
                Work
              </div>
            </label>

            <label>
              <input type="radio" name="category" value="personal" v-model="input_category"/>
              <span class="bubble personal"></span>
              <div>
                Personal
              </div>
            </label>

            <label>
              <input type="radio" name="category" value="prod" v-model="input_category"/>
              <span class="bubble prod"></span>
              <div>
                Productivity
              </div>
            </label>

            <label>
              <input type="radio" name="category" value="other" v-model="input_category"/>
              <span class="bubble other"></span>
              <div>
                Other
              </div>
            </label>
          </div>

          <input class="buttons" type="submit" value="Add todo"/>
        </form>

        <form @submit.prevent="clearAllTasks">
          <input class="buttons clear-all-button" type="submit" value="Clear all tasks"/>
        </form> 
      </section>

      <section class="todo-list">
          <div class="list">
            <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
              <label>
                <input type="checkbox" v-model="todo.done"/>
                <span :class="`bubble ${todo.category}`"></span>
              </label>

              <div class="todo-content">
                  <input type="text" v-model="todo.content"/>
              </div>

              <div class="actions">
                <button class="delete" @click="removeTodo(todo)">Delete</button>
              </div>
            </div>
          </div>
      </section>
  </main>
</template>