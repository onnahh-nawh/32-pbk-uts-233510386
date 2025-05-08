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


    </script>
  </div>
</template>