<template>
  <div>
    <h1>{{ message }}</h1>
    <button @click="fetchUsers">Fetch Users</button>
    <ul>
      <li v-for="user in users" :key="user._id">{{ user.firstName }} {{ user.lastName }}</li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      message: '',
      users: [],
    };
  },
  created() {
    this.login();
  },
  methods: {
    async login() {
      try {
        const response = await axios.post('/api/root/login', {
          login: 'root',
          password: '123'
        });
        this.message = response.data.message;
      } catch (error) {
        console.error('Login error:', error);
      }
    },
    async fetchUsers() {
      try {
        const response = await axios.get('/api/root/users');
        this.users = response.data;
      } catch (error) {
        console.error('Error fetching users:', error);
      }
    },
  },
};
</script>
  