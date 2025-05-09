<template>
  <div>
    <header>
      <h1>{{ listTitle }}</h1>
    </header>

    <div class="card">
      <button v-if="!showAddForm" @click="toggleAddForm" class="add-task-button">
        <span class="plus-icon">+</span> Tambahkan Kegiatan Baru
      </button>

      <div v-if="showAddForm" class="form-container">
        <div class="form-header">
          <h3>Tambah Kegiatan Baru</h3>
          <button @click="toggleAddForm" class="close-button">×</button>
        </div>

        <div class="input-row">
          <input v-model="newTask.text" type="text" placeholder="Masukkan kegiatan baru..." class="task-input" />
        </div>

        <div class="input-row">
          <input v-model="newTask.description" type="text" placeholder="Deskripsi kegiatan (opsional)" class="task-input" />
        </div>

        <div class="input-row date-row">
          <input v-model="newTask.date" type="date" class="date-input" />
          <button @click="addTask" class="add-button">Tambah</button>
        </div>
      </div>

      <ul class="task-list">
        <li v-for="task in filteredTasks" :key="task.id" class="task-item">
          <div class="task-content">
            <input type="checkbox" :checked="task.completed" @change="toggleTaskStatus(task.id)" class="task-checkbox" />
            <div class="task-details">
              <div class="task-header">
                <span :class="{ 'completed': task.completed }" class="task-text">{{ task.text }}</span>
                <span v-if="task.date" class="task-date" :class="{ 'overdue': isOverdue(task) }">{{ formatDate(task.date) }}</span>
              </div>
              <p v-if="task.description" class="task-description" :class="{ 'completed': task.completed }">{{ task.description }}</p>
            </div>
          </div>
          <button @click="deleteTask(task.id)" class="delete-button">Hapus</button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const listTitle = ref('List Harian Gwh');
const showAddForm = ref(false);
const tasks = ref([]);
const newTask = ref({
  text: '',
  description: '',
  date: '',
  completed: false
});
const filterType = ref('all');

onMounted(() => {
  const savedTasks = localStorage.getItem('tasks');
  if (savedTasks) {
    tasks.value = JSON.parse(savedTasks);
  }
});

const saveTasks = () => {
  localStorage.setItem('tasks', JSON.stringify(tasks.value));
};

const addTask = () => {
  if (newTask.value.text.trim()) {
    tasks.value.push({
      id: Date.now(),
      text: newTask.value.text.trim(),
      description: newTask.value.description.trim(),
      date: newTask.value.date,
      completed: false
    });
    newTask.value = { text: '', description: '', date: '', completed: false };
    showAddForm.value = false;
    saveTasks();
  }
};

const toggleAddForm = () => {
  showAddForm.value = !showAddForm.value;
  if (!showAddForm.value) {
    newTask.value = { text: '', description: '', date: '', completed: false };
  }
};

const toggleTaskStatus = (id) => {
  const task = tasks.value.find(task => task.id === id);
  if (task) {
    task.completed = !task.completed;
    saveTasks();
  }
};

const filteredTasks = computed(() => {
  if (filterType.value === 'active') {
    return tasks.value.filter(task => !task.completed);
  }
  return tasks.value;
});
</script>
