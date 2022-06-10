<script setup>
  import { ref, onMounted, computed, watch } from 'vue'

  const todos = ref([])
  const name = ref('')

  const input_content = ref('')
  const input_category = ref(null)

  const todos_asc = computed(() => todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt
  }))

  const addTodo = () => {
    if(input_content.value.trim() === '' || input_category.value === null) {
      return
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

  const removeTodo = (index) => {
    todos.value = todos.value.filter(i => i !== index)
  }

  watch(todos, newValue => {
    localStorage.setItem('todos', JSON.stringify(newValue))
  }, { deep: true })

  watch(name, (val) => {
    localStorage.setItem('name', val)
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
        Bienvenido <input type="text" placeholder="Tu nombre" v-model="name">
      </h2>
    </section>

    <section class="create-todo">
      <h3>AÑADIR NUEVA NOTA</h3>

      <form @submit.prevent="addTodo">
        <h4>Cosas por hacer</h4>
        <input type="text" placeholder="E.j. Tomar agua" v-model="input_content">
        <h4>Categoría</h4>

        <div class="options">
          <label>
            <input type="radio" name="category" id="category1" value="business" v-model="input_category" />
            <span class="bubble business"></span>
            <div>Negocios</div>
          </label>
          <label>
            <input type="radio" name="personal" id="personal1" value="personal" v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        
        <input type="submit" value="Añadir" />
      </form>
    </section>

    <section class="todo-list">
      <h3>Lista</h3>
      <div class="list">
        <div v-for="todo in todos_asc"  v-bind:key="todo" :class="`todo-item ${todo.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>  
          </label>          

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Borrar</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>
