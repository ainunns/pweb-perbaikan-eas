<script setup>
import { ref } from "vue";
const email = ref("");
const password = ref("");
const id1 = ref("");
const email1 = ref("");
const id2 = ref("");
const email2 = ref("");
const users = ref([]);
const chats = ref([]);
const chat = ref();

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

async function getChats() {
  const res = await fetch("http://localhost:3000/api/chats", {
    method: "GET",
    credentials: "include",
  });

  const json = await res.json();
  console.log(json);
  chats.value = json.docs;
}

async function sendChat() {
  const res = await fetch("http://localhost:3000/api/chats", {
    method: "POST",
    headers: {
      "Content-Type": "application/json",
    },
    body: JSON.stringify({
      from: id1.value,
      to: id2.value,
      message: chat.value,
    }),
  });

  getChats();
}
</script>

<template>
  Email: {{ email }}<br />
  Password: {{ password }}<br /><br />
  Id1: {{ id1 }}<br />
  Email1: {{ email1 }}<br /><br />
  Id2: {{ id2 }}<br />
  Email2: {{ email2 }}<br /><br />
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
        <a
          @click="
            id2 = user.id;
            email2 = user.email;
            getChats();
          "
          >{{ user.email }}</a
        >
      </li>
    </ul>
    <br /><br />

    Chat with {{ email2 }} <br /><br />
    <div v-for="chat in chats">
      <div v-if="chat.from.id == id1 && chat.to.id == id2">
        <div>
          {{ chat.from.email }} -> {{ chat.to.email }} : {{ chat.message }}
        </div>
      </div>
      <div v-if="chat.from.id == id2 && chat.to.id == id1">
        <div>
          {{ chat.from.email }} -> {{ chat.to.email }} : {{ chat.message }}
        </div>
      </div>
    </div>
  </div>
  <br />
  <input v-model="chat" />
  <button @click="sendChat()">Send</button>
</template>
