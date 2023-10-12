<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const tasks = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)
const input_deadline = ref(null);

const tasks_asc = computed(() => tasks.value.sort((a, b) => {
  return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
  localStorage.setItem('name', newVal)
})

watch(tasks, (newVal) => {
  localStorage.setItem('tasks', JSON.stringify(newVal))
}, {
  deep: true
})

const addTask = () => {
  if (name.value === '') {
    alert('Please enter a name.');
    return;
  }

  if (input_content.value.trim() === '') {
    alert('Please write a task');
    return;
  }

  if (input_category.value === null) {
    alert('Please choose a category.');
    return;
  }

  const currentTime = new Date().getTime();

  const selectedDeadline = new Date(input_deadline.value).getTime();


  if (selectedDeadline < currentTime) {
    alert('Please select a deadline in the future.');
    return;
  }

  const newTask = {
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: currentTime,
    deadline: input_deadline.value,
    priority: "medium",
  };

  tasks.value.push(newTask);
};


const removeTask = (task) => {
  tasks.value = tasks.value.filter((t) => t !== task)
}

onMounted(() => {
  name.value = localStorage.getItem('name') || ''
  tasks.value = JSON.parse(localStorage.getItem('tasks')) || []
})
</script>

<template>
  <main class="app">

    <section class="greeting">
      <h2 class="title">
        Hello, <input type="text" id="name" placeholder="Name here" v-model="name">
      </h2>
    </section>

    <section class="create-task">
      <h3>MANAGE YOUR TASK</h3>

      <form id="new-task-form" @submit.prevent="addTask">
        <h4>What are your tasks for today?</h4>
        <input type="text" name="content" id="content" placeholder="e.g. lunch with Steve" v-model="input_content" />

        <h4>Pick a category</h4>
        <div class="options">

          <label>
            <input type="radio" name="category" id="category1" value="work" v-model="input_category" />
            <span class="bubble work"></span>
            <div>Work</div>
          </label>

          <label>
            <input type="radio" name="category" id="category2" value="private" v-model="input_category" />
            <span class="bubble private"></span>
            <div>Private</div>
          </label>

          <label>
            <input type="date" name="deadline" id="deadline" placeholder="Select a deadline" v-model="input_deadline" />
            <div>Deadline</div>
          </label>

        </div>

        <input type="submit" value="Add task" />
      </form>
    </section>

    <section class="task-to-manage">
      <h3>TASKS TODAY</h3>
      <div class="list" id="tasks-to-manage">

        <div v-for="task in tasks_asc" :class="`task ${task.done && 'done'}`">
          <label>
            <input type="checkbox" v-model="task.done" />
            <span :class="`bubble ${task.category == 'work'
              ? 'Work'
              : 'Private'
              }`"></span>
          </label>

          <div class="task-content">
            <input type="text" v-model="task.content" />
            <p v-if="task.deadline">Deadline: {{ task.deadline }}</p>

            <!-- Display the priority -->
            <p>Priority: {{ task.priority }}</p>
            <!-- Add the <select> element for priority here -->
            <select v-model="task.priority">
              <option value="high">High</option>
              <option value="medium">Medium</option>
              <option value="low">Low</option>
            </select>
          </div>

          <div class="actions">
            <button class="delete" @click="removeTask(task)">Delete</button>
          </div>
        </div>

      </div>
    </section>

  </main>
</template>
