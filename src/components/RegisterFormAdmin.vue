<template>
  <div>
    <div v-if="!isLoggedIn" class="register-form">
      <img class="logo" src="../assets/logo.png" alt="Logo" />

      <form @submit.prevent="sendRequest" class="form-group">
        <div class="form-el">
          <label for="login">Логин:</label>
          <input v-model="login" type="text" id="login" required />
        </div>
        <div class="form-el">
          <label for="password">Пароль:</label>
          <input v-model="password" type="password" id="password" required />
        </div>
        <button type="submit">Войти</button>
      </form>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      login: "",
      password: "",
      isLoggedIn: false,
      message: ""
    };
  },
  methods: {
    async sendRequest() {
      const options = {
        method: 'POST',
        url: 'http://localhost:3000/root/login',
        headers: {
          cookie: 'root-token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpcCI6Ijo6ZmZmZjoxNzIuMTguMC4xIiwiaWF0IjoxNzE5MzM2NjUyLCJleHAiOjE3MTk0MjMwNTJ9.S6jV4aYQG7LHbGybxggwmWMLgMnprv2qANirt2PWIqk',
          'Content-Type': 'application/json',
          'User-Agent': 'insomnia/9.2.0'
        },
        data: { login: this.login, password: this.password }
      };

      try {
        const response = await axios(options);
        console.log('Response:', response.data);

        if (response.data.message === 'Logged in successfully') {
          this.isLoggedIn = true;
          this.message = response.data.message;
          this.$emit('login-success'); // Вызываем событие для передачи успешного входа в родительский компонент
        } else {
          console.log('Authentication failed');
          // Дополнительная логика для обработки неуспешной аутентификации
        }
      } catch (error) {
        console.error('Error:', error);
        // Добавьте здесь логику для обработки ошибки
      }
    }
  }
};
</script>


<style scoped>
.logo {
    margin-bottom: 20px;
    margin-top: 100px;
  justify-content: center;
  align-items: center;
  border-radius: 50%;
}

.register-form {

  display: flex;
  flex-direction: column;
  justify-self: center;
  align-items: center;
  
  


  max-width: 500px;
  margin: 0 auto;
  padding: 20px;

  background-color: #fff;
}

.form-group {
  margin-top: 20px;
}

.form-el {
  display: flex;
  align-items: center;
  margin-bottom: 10px;
}

label {
  width: 100px;
  margin-right: 10px;
  font-weight: bold;
}

input[type="text"],
input[type="password"] {
  flex: 1; /* Занимать всё доступное пространство */
  padding: 8px;
  box-sizing: border-box;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  width: 100%;
  padding: 10px;
  background-color: #ff0000;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-top: 10px; /* Добавлено для отступа между формой и кнопкой */
}

button:hover {
  background-color: #cc0000;
}
</style>
