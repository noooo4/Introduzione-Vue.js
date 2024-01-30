<!-- SCRIPT -->
<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return a.createdAt - b.createdAt
}))

// Per salvare il nome in LocalStorage
watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

// Per salvare il DaFare in LocalStorage
watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, {
  deep: true
})

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime()
  })

  input_content.value = ''
  input_category.value = null
}

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo)
}


//  Per estrarre dal LocalStorage
onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>



<!-- TEMPLATE -->
<template>
  <main class="app">

    <!-- Sezione Benvenuto -->
    <section class="greeting">
      <h2 class="title">
        Ciao! Benvenuto, <input type="text" id="name" placeholder="Name here" v-model="name">
      </h2>
    </section>

    <!-- Sezione Crea Lista -->
    <section class="create-todo">
      <h3>DAFARE</h3>

      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>Cosa devi fare?</h4>
        <input type="text" name="content" id="content" placeholder="es. Andare a casa" v-model="input_content" />

        <h4>Sciegli una categoria</h4>
        <div class="options">

          <!-- Opzione Business -->
          <label>
            <input type="radio" name="category" id="category1" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>Lavoro</div>
          </label>
          <!-- Opzione Casa -->
          <label>
            <input type="radio" name="category" id="category1" value="home" v-model="input_category" />
            <span class="bubble home"></span>
            <div>Casa</div>
          </label>
          <!-- Opzione Personale -->
          <label>
            <input type="radio" name="category" id="category2" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personlale</div>
          </label>

        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">

        <div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category == 'business'
                ? 'business'
                : 'personal'
              }`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <!-- Per Cancellare il da fare -->
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Cancellare</button>
          </div>
        </div>

      </div>
    </section>

  </main>
</template>

