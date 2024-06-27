<template>
  <div id="admin-panel">
    <h2>Список пользователей</h2>
    <div class="users-list">
      <div class="user-card" v-for="user in users" :key="user._id">
        <p class="user-card-text">
          {{ user.lastName }} {{ user.firstName }} {{ user.patronymic }}
        </p>
        <button @click="deleteUser(user._id)" class="user-card-delete">
          Удалить
        </button>
      </div>
    </div>

    <h2 class="addUser-text">Добавить пользователя</h2>
    <form @submit.prevent="addUser">
      <div>
        <input class="addUser-info"
          type="text"
          placeholder="Имя"
          v-model="newUser.firstName"
          id="firstName"
          required
        />
      </div>
      <div>
        <input class="addUser-info"
          type="text"
          placeholder="Фамилия"
          v-model="newUser.lastName"
          id="lastName"
          required
        />
      </div>
      <div>
        <input class="addUser-info"
          type="text"
          placeholder="Отчество"
          v-model="newUser.patronymic"
          id="patronymic"
          required
        />
      </div>
      <div>
        <input class="addUser-info"
          type="text"
          placeholder="Логин"
          v-model="newUser.login"
          id="login"
          required
        />
      </div>
      <div>
        <input class="addUser-info"
          type="password"
          placeholder="Пароль"
          v-model="newUser.password"
          id="password"
          required
        />
      </div>
      <div class="addUser-button">   
      <button type="submit" class="addUser-button-add">Добавить</button>
      <button @click="logout" class="addUser-button-logout">Выйти</button>
      </div>
    </form>

 
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
        password: "",
      },
    };
  },
  methods: {
    async fetchUsers() {
      try {
        const response = await axios.get(
          import.meta.env.VITE_BACKAND_URL + "/root/users",
          {
            withCredentials: true,
            headers: {
              "Content-Type": "application/json",
            },
          }
        );
        this.users = response.data;
      } catch (error) {
        console.error("Ошибка получения пользователей:", error);
      }
    },
    async deleteUser(userId) {
      try {
        await axios.get(
          import.meta.env.VITE_BACKAND_URL + "/root/user/delete",
          {
            params: { id: userId },
            withCredentials: true,
            headers: {
              "Content-Type": "application/json",
            },
          }
        );
        this.fetchUsers();
      } catch (error) {
        console.error("Ошибка удаления пользователя:", error);
      }
    },
    async addUser() {
      try {
        await axios.post(
          import.meta.env.VITE_BACKAND_URL + "/root/user/add",
          this.newUser,
          {
            withCredentials: true,
            headers: {
              "Content-Type": "application/json",
            },
          }
        );
        this.newUser = {
          firstName: "",
          lastName: "",
          patronymic: "",
          login: "",
          password: "",
        };
        this.fetchUsers();
      } catch (error) {
        console.error("Ошибка добавления пользователя:", error);
      }
    },
    async logout() {
      try {
        await axios.get(import.meta.env.VITE_BACKAND_URL + "/root/logout", {
          withCredentials: true,
          headers: {
            "Content-Type": "application/json",
          },
        });
        this.$emit("logout");
      } catch (error) {
        console.error("Ошибка выхода:", error);
      }
    },
  },
  mounted() {
    this.fetchUsers();
  },
};
</script>
<style scoped>
#admin-panel {
  padding: 20px;
  max-width: 600px;
  margin: 0 auto;
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

.users-list {
  max-height: 400px;
  overflow-y: scroll;
  margin-bottom: 20px;
  scrollbar-width: none; /* Firefox */
  -ms-overflow-style: none;  /* Internet Explorer 10+ */
}

.user-card {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  border: 1px solid black;
  border-radius: 4px;
  margin-bottom: 10px;
  background-color: #f9f9f9;
}



.addUser-info {
  margin-bottom: 20px;
}

.user-card-text {
  width: 80%;
  text-align: left;
}

.user-card-delete {
  align-items: center;
  background-color: #cc3333;
    border-color: #cc3333;
  border-radius: 5px;
  color: white;
  width: 90px;
  height: 35px;
  cursor: pointer;
  
}


.user-card-delete:hover {
  background-color: #e60000;
  cursor: pointer;
  border-color: #e60000;
}

h2 {
  margin-bottom: 20px;
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
  width: 120px;
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

.add-button {
  width: 100%;
  padding: 10px;
  background-color: #4caf50;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-top: 10px;
}

.add-button:hover {
  background-color: #45a049;
}

.logout-button {
  width: 100%;
  padding: 10px;
  background-color: #ff0000;
  border-color: #ff0000;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  margin-top: 20px;
}

.logout-button:hover {
  background-color: #cc0000;
    cursor: pointer;
}

.addUser-button-add {
  align-items: center;
  justify-content: center;
  width: 80px;
  height: 35px;
  border-radius: 5px;
  margin-right: 70%;
  color: #fff;
  background-color: #0066ff;
  border-color: #0066ff;
    cursor: pointer;

}

.addUser-button-add:hover {
  background-color: #3333ff;
    color: #fff;
    cursor: pointer;
}

.addUser-button-logout {
  align-items: center;
  justify-content: center;
  width: 80px;
  height: 35px;
  border-radius: 5px;
  background-color: #33cc33 ;
  border-color: #33cc33 ;
  cursor: pointer;
    color: #fff;
}

.addUser-button-logout:hover {
  background-color: #49aa33;
    cursor: pointer;  
    color: #fff;
}

</style>
