<script setup>
import { ref, onMounted, computed, watch } from 'vue';
import DarkModeToggle from './components/DarkModeToggle.vue';

const todos = ref([]);
const name = ref('');
let usertheme = ref('');
let currentTheme = ref('');

const input_content = ref('');
const input_category = ref(null);

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return;
  }
  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    createdAT: new Date().getTime(),
    done: false,
  });
  input_content.value = '';
  input_category.value = null;
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

const todos_asc = computed(() =>
  todos.value.sort((a, b) => a.createdAT - b.createdAT)
);

watch(
  todos,
  (newVal) => {
    localStorage.setItem('todos', JSON.stringify(newVal));
  },
  {
    deep: true,
  }
);

watch(name, (newVal) => {
  localStorage.setItem('name', newVal);
});

onMounted(() => {
  name.value = localStorage.getItem('name') || '';
  todos.value = JSON.parse(localStorage.getItem('todos')) || [];
});

const toggleDarkMode = () => {
  usertheme.value = usertheme.value === 'light' ? 'dark' : 'light';
  localStorage.setItem('theme', usertheme.value);
  currentTheme.value = usertheme.value;
  if (currentTheme.value === 'dark') {
    document.querySelector('body').setAttribute('style', 'background:#3852a0');
  }else {
    document.querySelector('body').setAttribute('style', 'background:#EEE');
  }
};
</script>

<template>
  <main class="app" v-bind:class="currentTheme">
    <div class="app-container">
      <section class="greeting">
        <div class="dark-mode">
          <DarkModeToggle
            :class="{ active: currentTheme === 'dark' }"
            @click="toggleDarkMode"
          />
        </div>
        <h2 class="title">
          What's up,
          <input v-model="name" type="text" placeholder="Name here" />
        </h2>
      </section>

      <section class="create-todo">
        <h3>CREATE A TODO</h3>
        <form @submit.prevent="addTodo">
          <h4>What's on your todo list?</h4>
          <input
            type="text"
            placeholder="e.g make a video"
            v-model="input_content"
          />

          <h4>Pick a category</h4>

          <div class="options">
            <label>
              <input
                type="radio"
                name="category"
                value="work"
                v-model="input_category"
              />
              <span class="bubble work"></span>
              <div>work</div>
            </label>
            <label>
              <input
                type="radio"
                name="category"
                value="personal"
                v-model="input_category"
              />
              <span class="bubble personal"></span>
              <div>personal</div>
            </label>
          </div>
          <input type="submit" value="Add Todo" />
        </form>
      </section>

      <section class="todo-list">
        <h3>TODO LIST</h3>
        <div class="list">
          <div
            v-for="todo in todos_asc"
            :class="`todo-item ${todo.done && 'done'}`"
          >
            <label>
              <input type="checkbox" v-model="todo.done" />
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
    </div>
  </main>
</template>
