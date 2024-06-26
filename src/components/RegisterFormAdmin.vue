<template>
  <div class="form-container">
    <div class="form-content">
      <h2>Добавление новых пользователей</h2>
      <form @submit.prevent="addUser">
        <div class="form-group">
          <label for="firstName">Имя:</label>
          <input v-model="firstName" type="text" id="firstName" required />
        </div>
        <div class="form-group">
          <label for="lastName">Фамилия:</label>
          <input v-model="lastName" type="text" id="lastName" required />
        </div>
        <div class="form-group">
          <label for="patronymic">Отчество:</label>
          <input v-model="patronymic" type="text" id="patronymic" required />
        </div>
        <div class="form-group">
          <label for="login">Логин:</label>
          <input v-model="login" type="text" id="login" required />
        </div>
        <div class="form-group">
          <label for="password">Пароль:</label>
          <input v-model="password" type="password" id="password" required />
        </div>
        <button type="submit">Добавить пользователя</button>
      </form>
    </div>

    <div class="user-list">
      <h2>Список пользователей</h2>
      <div class="user-list-container">
        <ul v-if="users.length">
          <li v-for="user in users" :key="user._id">
            {{ user.firstName }} {{ user.lastName }}
            <button @click="confirmDelete(user._id)">Удалить</button>
          </li>
        </ul>
        <p v-else>Нет пользователей</p>
      </div>
    </div>

    <!-- Кнопка выхода из root -->
    <button class="logout-button" @click="logout">Выйти из Root</button>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      users: [],
      firstName: "",
      lastName: "",
      patronymic: "",
      login: "",
      password: "",
      token:
        "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpcCI6Ijo6ZmZmZjoxNzIuMTguMC4xIiwiaWF0IjoxNzE5MTY0MDEyLCJleHAiOjE3MTkyNTA0MTJ9.dKgQaUL5CZuzEe4_BteLllQdjKP0WrF63uRJBH0_VaI",
    };
  },
  created() {
    this.fetchUsers();
  },
  methods: {
    async fetchUsers() {
      try {
        const response = await axios.get("http://localhost:3000/root/users", {
          headers: {
            Authorization: `Bearer ${this.token}`,
          },
        });
        this.users = response.data;
      } catch (error) {
        console.error("Error fetching users:", error);
      }
    },
    async addUser() {
      if (!this.firstName || !this.lastName || !this.login || !this.password) {
        alert("Заполните все обязательные поля!");
        return;
      }
      try {
        const newUser = {
          firstName: this.firstName,
          lastName: this.lastName,
          patronymic: this.patronymic,
          login: this.login,
          password: this.password,
        };
        await axios.post("http://localhost:3000/root/user/add", newUser, {
          headers: {
            Authorization: `Bearer ${this.token}`,
          },
        });
        this.fetchUsers();
        this.firstName = "";
        this.lastName = "";
        this.patronymic = "";
        this.login = "";
        this.password = "";
      } catch (error) {
        console.error("Error adding user:", error);
      }
    },
    async confirmDelete(userId) {
      const confirmed = confirm("Вы уверены, что хотите удалить пользователя?");
      if (confirmed) {
        await this.deleteUser(userId);
      }
    },
    async deleteUser(userId) {
      try {
        await axios.delete(
          `http://localhost:3000/root/user/delete?id=${userId}`,
          {
            headers: {
              Authorization: `Bearer ${this.token}`,
            },
          }
        );
        this.fetchUsers();
      } catch (error) {
        console.error("Error deleting user:", error);
      }
    },
    async logout() {
      try {
        const form = new FormData();
        form.append('{\n"login": "root",\n"password": "root"\n}\n', "");
        const options = {
          method: "GET",
          url: "http://localhost:3000/root/logout",
          params: { "": ["", ""] },
          headers: {
            cookie:
              "root-token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpcCI6Ijo6ZmZmZjoxNzIuMTguMC4xIiwiaWF0IjoxNzE5NDA5NjMzLCJleHAiOjE3MTk0OTYwMzN9.5cwkpBhjCKLv2HerMHbjONLhSvj_Nbcfmm6rkq-dvRg",
            "Content-Type":
              "multipart/form-data; boundary=---011000010111000001101001",
            "User-Agent": "insomnia/9.2.0",
          },
          data: "[form]",
        };
        await axios.request(options);
        // Можно добавить логику для перенаправления на страницу авторизации или другое действие после выхода
        console.log("Logged out successfully");
      } catch (error) {
        console.error("Error logging out:", error);
      }
    },
  },
};
</script>

<style scoped>
.form-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
  background-color: #f0f0f0;
}

.form-content {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  width: 400px;
  margin-bottom: 20px;
}

.form-group {
  display: flex;
  align-items: center;
  margin-bottom: 15px;
}

.form-group label {
  width: 120px;
  margin-right: 10px;
  text-align: right;
}

.form-group input {
  flex: 1;
  padding: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  width: 100%;
  padding: 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}

button[type="button"] {
  background-color: #dc3545;
}

button[type="button"]:hover {
  background-color: #c82333;
}

.logout-button {
  width: 100%;
  padding: 10px;
  background-color: #dc3545;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-top: 10px;
}

.logout-button:hover {
  background-color: #c82333;
}

.user-list {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  width: 400px;
}

.user-list-container {
  max-height: 200px; /* Ограничиваем высоту контейнера */
  overflow-y: auto; /* Добавляем вертикальную прокрутку */
}

.user-list ul {
  list-style-type: none;
  padding: 0;
  margin: 0;
}

.user-list li {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px 0;
  border-bottom: 1px solid #ccc;
}

.user-list button {
  background-color: #dc3545;
  color: white;
  border: none;
  border-radius: 4px;
  padding: 5px 10px;
  cursor: pointer;
}

.user-list button:hover {
  background-color: #c82333;
}

.user-list p {
  text-align: center;
  font-style: italic;
}
</style>
