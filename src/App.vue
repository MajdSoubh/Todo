<script setup>
import { ref, watch, computed, onMounted } from "vue";
const name = ref("");
const category = ref("");
const content = ref("");
const tasks = ref([]);
const tasksList = computed(() =>
  tasks.value.sort((a, b) => b.createAt - a.createAt)
);

onMounted(() => {
  name.value = localStorage.getItem("name") || "";
  tasks.value = JSON.parse(localStorage.getItem("tasks") || "[]");
});
watch(name, (newName) => {
  localStorage.setItem("name", newName);
});
watch(
  tasks,
  (newTasks) => {
    localStorage.setItem("tasks", JSON.stringify(newTasks || []));
  },
  { deep: true }
);

function addTask() {
  if (content.trim == "") return;
  tasks.value.push({
    category: category.value,
    content: content.value,
    done: false,
    createAt: Date.now(),
  });
  category.value = "";
  content.value = "";
}
function removeTask(task) {
  tasks.value = tasks.value.filter((t) => task != t);
}
</script>
<template>
  <section class="greeting">
    <h2>What's up,</h2>
    <input type="text" v-model="name" placeholder="Your name" />
  </section>
  <section class="create-task">
    <h3>CREATE TASK</h3>
    <form @submit.prevent="addTask">
      <div class="form-group">
        <label>What's on your mind ?</label>
        <input type="text" v-model="content" placeholder="e.g. play paino" />
      </div>
      <div class="form-group">
        <label>Pick a category</label>
        <div class="category-group">
          <div class="category">
            <label>
              <input
                type="radio"
                value="business"
                v-model="category"
                name="category"
              />
              <span class="bubble business"></span>
              <span>Business</span>
            </label>
          </div>
          <div class="category">
            <label>
              <input
                type="radio"
                value="personal"
                v-model="category"
                name="category"
              />
              <span class="bubble personal"></span>
              <span>Personal</span>
            </label>
          </div>
        </div>
      </div>
      <button class="submit-btn" type="submit">Add Task</button>
    </form>
  </section>
  <section v-if="tasks.length > 0" class="tasks-table">
    <h3>TASKS</h3>
    <ul>
      <li v-for="task in tasksList" :key="task.createAt" class="task-info">
        <label>
          <input v-model="task.done" type="checkbox" />
          <span :class="`bubble ${task.category}`"></span>
        </label>
        {{ task.content }}
        <button @click="removeTask(task)">Remove</button>
      </li>
    </ul>
  </section>
</template>

<style scoped></style>
