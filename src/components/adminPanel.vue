<template>
  <div id="admin-panel">
    <h2>Список пользователей</h2>
    <div class="users-list">
      <div class="user-card" v-for="user in users" :key="user._id">
        <p>{{ user.lastName }} {{ user.firstName }} {{ user.patronymic }}</p>
        <button @click="deleteUser(user._id)">Удалить</button>
      </div>
    </div>

    <h2>Добавить пользователя</h2>
    <form @submit.prevent="addUser">
      <div>
        <label for="firstName">Имя:</label>
        <input type="text" v-model="newUser.firstName" id="firstName" required />
      </div>
      <div>
        <label for="lastName">Фамилия:</label>
        <input type="text" v-model="newUser.lastName" id="lastName" required />
      </div>
      <div>
        <label for="patronymic">Отчество:</label>
        <input type="text" v-model="newUser.patronymic" id="patronymic" required />
      </div>
      <div>
        <label for="login">Логин:</label>
        <input type="text" v-model="newUser.login" id="login" required />
      </div>
      <div>
        <label for="password">Пароль:</label>
        <input type="password" v-model="newUser.password" id="password" required />
      </div>
      <button type="submit">Добавить</button>
    </form>

    <button @click="logout">Выйти</button>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "AdminPanel",
  data() {
    return {
      users: [],
      newUser: {
        firstName: "",
        lastName: "",
        patronymic: "",
        login: "",
        password: ""
      }
    };
  },
  methods: {
    async fetchUsers() {
      try {
        const response = await axios.get('http://localhost:3000/root/users', {
          headers: {
            cookie: 'root-token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpcCI6Ijo6ZmZmZjoxNzIuMTguMC4xIiwiaWF0IjoxNzE5NDE0NjIzLCJleHAiOjE3MTk1MDEwMjN9.jyHLFkUzb4xZ2Xd4aB8p-iz-KSGQ2nvRgBuZjg4NJYg',
            'Content-Type': 'application/json',

          }
        });
        this.users = response.data;
      } catch (error) {
        console.error("Ошибка получения пользователей:", error);
      }
    },
    async deleteUser(userId) {
      try {
        await axios.get('http://localhost:3000/root/user/delete', {
          params: { id: userId },
          headers: {
            cookie: 'root-token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpcCI6Ijo6ZmZmZjoxNzIuMTguMC4xIiwiaWF0IjoxNzE5NDE0NjIzLCJleHAiOjE3MTk1MDEwMjN9.jyHLFkUzb4xZ2Xd4aB8p-iz-KSGQ2nvRgBuZjg4NJYg',
            'Content-Type': 'application/json',

          }
        });
        this.fetchUsers();
      } catch (error) {
        console.error("Ошибка удаления пользователя:", error);
      }
    },
    async addUser() {
      try {
        await axios.post('http://localhost:3000/root/user/add', this.newUser, {
          headers: {
            cookie: 'root-token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpcCI6Ijo6ZmZmZjoxNzIuMTguMC4xIiwiaWF0IjoxNzE5NDE0NjIzLCJleHAiOjE3MTk1MDEwMjN9.jyHLFkUzb4xZ2Xd4aB8p-iz-KSGQ2nvRgBuZjg4NJYg',
            'Content-Type': 'application/json',

          }
        });
        this.newUser = { firstName: "", lastName: "", patronymic: "", login: "", password: "" };
        this.fetchUsers();
      } catch (error) {
        console.error("Ошибка добавления пользователя:", error);
      }
    },
    async logout() {
      try {
        await axios.get('http://localhost:3000/root/logout', {
          headers: {
            cookie: 'root-token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpcCI6Ijo6ZmZmZjoxNzIuMTguMC4xIiwiaWF0IjoxNzE5NDE0NjIzLCJleHAiOjE3MTk1MDEwMjN9.jyHLFkUzb4xZ2Xd4aB8p-iz-KSGQ2nvRgBuZjg4NJYg',
            'Content-Type': 'application/json',

          }
        });
        this.$emit('logout');
      } catch (error) {
        console.error("Ошибка выхода:", error);
      }
    }
  },
  created() {
    this.fetchUsers();
  }
};
</script>

<style scoped>
#admin-panel {
  padding: 20px;
}
.users-list {
  max-height: 400px;
  overflow-y: scroll;
  margin-bottom: 20px;
}
.user-card {
  display: flex;
  justify-content: space-between;
  padding: 10px;
  border: 1px solid #ccc;
  margin-bottom: 10px;
}
</style>
