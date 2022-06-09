<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const todos = ref([]);
const name = ref('');

const input_content = ref('');
const input_category = ref(null);

const addTodo = () => {};

const todos_asc = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt > a.createdAt;
  })
);

watch(name, (newVal) => {
  localStorage.setItem('name', newVal);
});

onMounted(() => {
  name.value = localStorage.getItem('name') || '';
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input v-model="name" type="text" placeholder="Name here" />
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
      </form>
    </section>
  </main>
</template>
