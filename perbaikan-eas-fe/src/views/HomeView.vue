<script setup>
import { ref } from "vue";
const email = ref("");
const password = ref("");
const id1 = ref("");
const email1 = ref("");
const users = ref([]);

async function login() {
  const res = await fetch("http://localhost:3000/api/users/login", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      email: email.value,
      password: password.value,
    }),
  });

  const json = await res.json();
  console.log(json);

  if (json && json.user) {
    id1.value = json.user.id;
    email1.value = json.user.email;
    getUsers();
  }
}

async function getUsers() {
  const res = await fetch("http://localhost:3000/api/users", {
    method: "GET",
    credentials: "include",
  });

  const json = await res.json();
  console.log(json);
  users.value = json.docs;
}
</script>

<template>
  Email: {{ email }}<br />
  Password: {{ password }}<br /><br />
  Id1: {{ id1 }}<br />
  Email1: {{ email1 }}<br /><br />
  <div v-if="id1 == ''">
    <h1>Login</h1>
    <input v-model="email" placeholder="email" /><br />
    <input v-model="password" placeholder="password" /><br />
    <button @click="login">Login</button>
  </div>
  <div v-else>
    <h1>Chat</h1>
    <ul>
      <li v-for="user in users" :key="user.id">
        <a>{{ user.email }}</a>
      </li>
    </ul>
  </div>
</template>
