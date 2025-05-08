<template>
  <div>
    <header>
      <h1>{{ listTitle }}</h1>
    </header>

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
      })
      const filterType = ref('all');

      onMounted(() => {
      const savedTasks = localStorage.getItem('tasks');
        if (savedTasks) {
          tasks.value = JSON.parse(savedTasks);
        }

      });

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



    });


      



    </script>
  </div>
</template>