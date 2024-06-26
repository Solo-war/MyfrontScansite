<template>
  <div>
    <div class="register-form">
      <img class="logo" src="../assets/logo.png" alt="Error" />

      <form @submit.prevent="register" class="form-group">
        <div class="form-el">
          <label for="login">Логин:</label>
          <input v-model="login" type="text" id="login" required />
        </div>
        <div class="form-el">
          <label for="password">Пароль:</label>
          <input v-model="password" type="password" id="password" required />
        </div>
        <button type="submit">Войти</button>
        <div class="insert"><p class="insertUser" @click="$emit('switch-form')">Вход для Admin</p></div>
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
    };
  },
  methods: {
    async register() {
      try {
        const response = await axios.post("http://localhost:3000/user/login", {
          login: this.login,
          password: this.password,
        });

        if (
          response.status === 200
        ) {
          this.$emit("authenticated");
          alert("Вы вошли как Пользователь");
        } else {
          alert("Неверный логин или пароль");
        }
      } catch (error) {
        console.error(error);
        alert("Произошла ошибка при входе");
      }
    },
  },
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
  flex: 1;
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
  margin-top: 10px;
}

button:hover {
  background-color: #cc0000;
}

.insert {
  width: 100%;
  text-align: right;
  box-sizing: border-box;
}

.insertUser {
  display: inline-block;
  text-align: right;
  width: 72px;
  color: grey;
  border-bottom: 1px solid grey;
  font-weight: 100;
  font-size: 12px;
}
</style>
