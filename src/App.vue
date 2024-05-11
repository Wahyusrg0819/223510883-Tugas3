<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])

const name = ref('')

const input_content = ref('')

const filterCompleted = ref(false)

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return b.createdAt - a.createdAt
}))

const filteredTodos = computed(() => {
  if (filterCompleted.value) {
    return todos_asc.value.filter(todo => !todo.done)
  } else {
    return todos_asc.value
  }
})


const addTodo = () => {
  if (input_content.value.trim() === '') {
    return
  }

  todos.value.push({
    content: input_content.value,
    done: false,
    createdAt: new Date().getTime()
  })

  input_content.value = ''
}

const removeTodo = todo => {
  const index = todos.value.findIndex(t => t === todo)
  if (index !== -1) {
    todos.value.splice(index, 1)
  }
}


watch(todos, newVal => {
  localStorage.setItem('todos', JSON.stringify(newVal))
}, { deep: true })

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

watch(filterCompleted, (newVal) => {
  localStorage.setItem('filterCompleted', newVal.toString()) 
})

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  todos.value = JSON.parse(localStorage.getItem('todos')) || []
  filterCompleted.value = JSON.parse(localStorage.getItem('filterCompleted')) || false
})
</script>

<template>

  <header>
    <h1>App TodoList-Crud Wahyu</h1>
  </header>

  <main class="app">
    <section class="create-todo">
      <h3>Buat Kegiatan</h3>

      <form @submit.prevent="addTodo">
        <h4>Apa isi daftar kegiatan mu?</h4>
        <input type="text" placeholder="contoh [membaca Buku]" v-model="input_content" />
        <input type="submit" value="Tambah Kegiatan" />
      </form>
    </section>

    <section class="todo-list">
      <h3>Daftar Kegiatan</h3>

      <div class="filter">
        <button @click="filterCompleted = !filterCompleted">{{ filterCompleted ? 'Tampilkan Semua' : 'Tampilkan Belum selesai' }}</button>
      </div>

      <div class="list">

        <div v-for="todo in filteredTodos" :key="todo.createdAt" :class="`todo-item ${todo.done && 'done'}`">

          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>

          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">⨉</button>
          </div>

        </div>

      </div>
    </section>

  </main>
</template>


<style scoped>
.filter {
  margin-bottom: 10px;
}

.filter button {
  background-color: #7900ff; /* Green */
  border: none;
  color: white;
  padding: 10px 20px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  cursor: pointer;
  border-radius: 5px;
}

.filter button:hover {
  background-color: #45a049;
}

/* Hide the default checkbox */
.todo-list input[type="checkbox"] {
  position: absolute;
  opacity: 0;
  cursor: pointer;
  height: 0;
  width: 0;
  border-radius: 5px;
}

.todo-list label {
  display: flex; /* Tambahkan flex untuk membuat label dan custom checkbox dalam satu baris */
  align-items: center; /* Tambahkan align-items untuk menengahkan custom checkbox vertikal */
  position: relative;
  cursor: pointer;
  font-size: 20px;
  user-select: none;
  border-radius: 5px;
  padding-left: 2em; /* Tambahkan padding untuk memberikan ruang bagi custom checkbox */
}

/* Create a custom checkbox */
.todo-list label .bubble {
  position: absolute;
  left: 0; /* Geser custom checkbox ke kiri agar berada di samping label */
  height: 1.3em;
  width: 1.3em;
  background-color: #ccc;
  border-radius: 5px;
}

/* When the checkbox is checked, add a blue background */
.todo-list input[type="checkbox"]:checked ~ .bubble {
  box-shadow: 3px 3px 0px rgb(183, 183, 183);
  transition: all 0.2s;
  opacity: 1;
  background-image: linear-gradient(45deg, rgb(100, 61, 219) 0%, rgb(217, 21, 239) 100%);
}

/* Create the checkmark/indicator (hidden when not checked) */
.todo-list label .bubble:after {
  content: "";
  position: absolute;
  opacity: 0;
  transition: all 0.2s;
}

/* Show the checkmark when checked */
.todo-list input[type="checkbox"]:checked ~ .bubble:after {
  opacity: 1;
  transition: all 0.2s;
}

/* Style the checkmark/indicator */
.todo-list label .bubble:after {
  content: "";
  position: absolute;
  left: 0.45em;
  top: 0.25em;
  width: 0.25em;
  height: 0.5em;
  border: solid white;
  border-width: 0 0.15em 0.15em 0;
  transform: rotate(45deg);
}


</style>