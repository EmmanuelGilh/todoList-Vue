<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_acend = computed(() => todos.value.sort((a,b) => {
  return b.createdAt - a.createdAt
}))

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return
    //tirar un modal sobre el problema
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime() 
  })

  input_content.value = ''
  input_category.value = null
}

const removeTodo = todo =>{
  todos.value = todos.value.filter(t => t !== todo)
}

watch(todos, newValue => {
  localStorage.setItem('todos', JSON.stringify(newValue))
}, { deep: true })

watch(name, (newValue) => {
  localStorage.setItem('name', newValue)
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
        Bienvenido <input type="text" 
        placeholder="Escribe tu nombre aquí" 
        v-model="name">
      </h2>
    </section>

    <section class="creator">
      <h3>Agrega una tarea:</h3>

      <form @submit.prevent="addTodo">
          <input 
            type="text" 
            name="content"
            id="content"
            placeholder="Ejemplo: Completar mis tareas..." 
            v-model="input_content"
          />

          <h4>Elige la categoría</h4>

          <div class="options">

            <label>
              <input
              type="radio"
              name="category"
              id="category1"
              value="business"
              v-model="input_category"
              >
              <span class="bubble business"></span>
              <div>Negocios</div>
            </label>
            
            <label>
              <input
              type="radio"
              name="category"
              id="category2"
              value="personal"
              v-model="input_category"
              >
              <span class="bubble personal"></span>
              <div>Personal</div>
            </label>

          </div>

          <input type="submit" value="Agregar a la lista"/>
      </form>
    </section>

    <section class="todoList">
      <h3>Lista de Tareas</h3>
      <div class="list" id="todoList">
        <div v-for="todo in todos_acend" :class="`todo-item ${todo.done && 'done'}`">

          <label>
            <input type="checkbox" v-model="todo.done"/>
            <span :class="`bubble ${
							todo.category == 'business' ? 'business' : 'personal'}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Borrar</button>
          </div>
        </div>

      </div>
    </section>

  </main>
</template>

