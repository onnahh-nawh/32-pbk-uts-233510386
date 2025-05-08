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
      const newTask = ref({ ... });
      const filterType = ref('all');

    </script>
  </div>
</template>