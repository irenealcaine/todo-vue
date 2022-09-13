<script setup>
  import { ref, onMounted, computed, watch } from 'vue'
  const todos = ref([])
  const name = ref('')
  const input_content = ref('')
  const input_category = ref(null)
  const todos_asc = computed(() => todos.value.sort((a,b) =>{
    return b.createdAt - a.createdAt
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
    <main class="py-4 px-6 text-white">

      <section class="md:w-3/5 mx-auto">
        <h1 class="text-3xl">Crea una nueva tarea</h1>

        <form id="new-todo-form" @submit.prevent="addTodo">

          <input
            type="text"
            name="content"
            id="content"
            placeholder=""
            v-model="input_content"
            class="mt-3 rounded-xl border-2 border-green-600 py-2 px-4 w-full h-24 text-gray-300 bg-gray-700"/>

          <h4 class="mt-6">Selecciona una categoría</h4>
          <div class="flex justify-between">

            <label class="flex flex-col w-1/2 items-center py-4 border-2 border-blue-300 rounded-xl my-2 mr-1 bg-blue-600 hover:cursor-pointer hover:bg-blue-500">
              <input
                type="radio"
                name="category"
                id="category1"
                value="business"
                v-model="input_category" />
              <div>Trabajo</div>
            </label>

            <label class="flex flex-col w-1/2 items-center py-4 border-2 border-orange-300 rounded-xl my-2 ml-1 bg-orange-600 hover:cursor-pointer hover:bg-orange-500">
              <input
                type="radio"
                name="category"
                id="category2"
                value="personal"
                v-model="input_category" />
              <div>Personal</div>
            </label>

          </div>

          <input type="submit" value="Agrega la tarea" class="font-bold bg-green-600 text-white rounded-xl py-2 px-4 hover:bg-green-500 hover:cursor-pointer w-full my-2"/>
        </form>
      </section>

      <section class="md:w-3/5 mx-auto">
        <h3 class="mt-6">Lista de tareas</h3>
        <div id="todo-list">

          <div
            v-for="todo in todos_asc"
            :class="`${todo.done ? 'bg-gray-600 text-gray-500' : todo.category === 'business' ? 'bg-blue-600' : 'bg-orange-600'} flex justify-between py-2 px-4 items-center rounded-xl mt-2 `"
            :key="todo.createdAt"
          >

          <div class="flex w-full">
            <label>
              <input type="checkbox" v-model="todo.done" class=""/>
            </label>

            <div class="w-full">
              <input type="text" v-model="todo.content" class="bg-transparent px-2 w-full"/>
            </div>
          </div>

            <div>
              <button class="bg-red-600 py-1 px-2 rounded text-white font-bold" @click="removeTodo(todo)">Borrar</button>
            </div>
          </div>

        </div>
      </section>

    </main>
  </template>
