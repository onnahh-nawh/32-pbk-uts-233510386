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

      <div class="filter-container">
        <button @click="filterType = 'all'" :class="{ active: filterType === 'all' }" class="filter-button">Semua</button>
        <button @click="filterType = 'active'" :class="{ active: filterType === 'active' }" class="filter-button">Belum Selesai</button>
      </div>

      <div v-if="filteredTasks.length === 0" class="empty-list">
        <p>Belum ada kegiatan {{ filterMessage }}</p>
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

      <div class="summary">
        <p>Total: {{ tasks.length }} kegiatan | Selesai: {{ completedCount }} | Belum: {{ activeCount }}</p>
      </div>

    <footer>
      <p>© 2025 UTS PBK Refiona Asri Rizky</p>
    </footer>

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

const filterMessage = computed(() => {
  return filterType.value === 'active' ? 'yang belum selesai' : '';
});

const completedCount = computed(() => {
  return tasks.value.filter(task => task.completed).length;
});

const activeCount = computed(() => {
  return tasks.value.filter(task => !task.completed).length;
});

const formatDate = (dateString) => {
  if (!dateString) return '';
  const date = new Date(dateString);
  return date.toLocaleDateString('id-ID', {
    weekday: 'short',
    day: 'numeric',
    month: 'short',
    year: 'numeric'
  });
};

const isOverdue = (task) => {
  if (!task.date || task.completed) return false;
  const today = new Date();
  today.setHours(0, 0, 0, 0);
  const taskDate = new Date(task.date);
  taskDate.setHours(0, 0, 0, 0);
  return taskDate < today;
};

</script>
