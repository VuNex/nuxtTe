<script setup lang="ts">
import { ref, onMounted } from 'vue';
import prisma from '@/lib/prisma'; // Убедитесь, что путь правильный

const users = ref<any>([]);
const newUser = ref({ name: '', email: '' });

const loadData = async () => {
  users.value = await prisma.user.findMany();
}

const addUser = async () => {
  if (newUser.value.name && newUser.value.email) {
    try {
      const user = await prisma.user.create({
        data: {
          name: newUser.value.name,
          email: newUser.value.email,
        },
      });
      users.value.push(user);
      newUser.value.name = '';
      newUser.value.email = '';
    } catch (error) {
      console.error('Ошибка при добавлении пользователя:', error);
    }
  }
}

onMounted(() => {
  loadData();
});
</script>

<template>
  <header>
    <nav>
      <ul>
        <li><NuxtLink to="/about">About</NuxtLink></li>
      </ul>
    </nav>
  </header>
  <div>
    <h1>About</h1>
    <p>Current route: {{ route.path }}</p>

    <div>
      <input v-model="newUser.name" placeholder="Имя" />
      <input v-model="newUser.email" placeholder="Email" />
      <button @click="addUser">Добавить пользователя</button>
    </div>

    <ul>
      <li v-for="user in users" :key="user.id">
        {{ user.name }} ({{ user.email }})
      </li>
    </ul>
  </div>
</template>