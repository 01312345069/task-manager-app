<template>
    <div>
      <button @click="showForm = !showForm">Toggle Add Task Form</button>
      <form v-if="showForm" @submit.prevent="addTask">
        <input v-model="newTask.name" required minlength="3" placeholder="Enter task name">
        <button type="submit">Add Task</button>
      </form>
      <div v-if="tasks.length === 0">No tasks available</div>
      <ul v-else>
        <li v-for="(task, index) in filteredTasks" :key="index" 
            :style="{ backgroundColor: task.completed ? 'lightgreen' : 'white', border: task.completed ? '2px solid red' : 'none' }">
          {{ task.name }}
          <input type="checkbox" v-model="task.completed" @change="saveTasks">
          <button @click="deleteTask(index)">Delete</button>
        </li>
      </ul>
      <button @click="toggleCompleted">Show/Hide Completed</button>
    </div>
  </template>
  
  <script setup>
  import { ref, computed, watch } from 'vue';
  
  const tasks = ref([]);
  const newTask = ref({ name: '', completed: false });
  const showForm = ref(false);
  const showCompleted = ref(true);
  
  function addTask() {
    if (newTask.value.name.length >= 3) {
      tasks.value.push({ ...newTask.value, completed: false });
      newTask.value.name = '';
      saveTasks();
    }
  }
  
  function deleteTask(index) {
    tasks.value.splice(index, 1);
    saveTasks();
  }
  
  function toggleCompleted() {
    showCompleted.value = !showCompleted.value;
  }
  
  const filteredTasks = computed(() => 
    showCompleted.value ? tasks.value : tasks.value.filter(task => !task.completed)
  );
  
  function saveTasks() {
    localStorage.setItem('tasks', JSON.stringify(tasks.value));
  }
  
  // Load tasks from localStorage on component mount
  if (localStorage.getItem('tasks')) {
    tasks.value = JSON.parse(localStorage.getItem('tasks'));
  }
  
  watch(tasks, () => {
    saveTasks();
  }, { deep: true });
  
  </script>
  
  <style scoped>
  li {
    margin-bottom: 10px;
    padding: 10px;
  }
  </style>