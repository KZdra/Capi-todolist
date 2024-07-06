<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

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

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
  <main class="app">
    <section class="greetings">
      <h2 class="title">Euy Cep , <input type="text" placeholder="Ngaran Maneh" v-model="name"></h2>
    </section>

    <section class="create-todo">
      <h3>Jieun Nu rek Dilaksanakeun</h3>

      <form @submit.prevent="addTodo">
        <h4>Aya naon nu di list</h4>
        <input type="text" placeholder="Misal Hayang Ngarit" v-model="input_content">
        
        <h4> Pilih Kategori</h4>
        <div class="options">
          <label>
            <input type="radio"  name="category" value="business" v-model="input_category"/>
            <span class="bubble bussiness"></span>
            <div>Bisnis</div>
          </label>
          <label>
            <input type="radio"  name="category" value="personal" v-model="input_category"/>
            <span class="bubble personal"></span>
            <div>Soranganneun</div>
          </label>
        </div>
        <input type="submit" value="Tambahkeun">
      </form>
  
    </section>
    <section class="todo-list">
			<h3>TODO LIST</h3>
			<div class="list" id="todo-list">

				<div v-for="(todo, index) in todos_asc" :key="index"  :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
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

