<template>
  <div id="app">
    <RegisterFormAdmin v-if="!isAdminAuthenticated" @login-success="handleLoginSuccess" />
    <ApiComponent v-if="isAdminAuthenticated" />
    <Header v-if="isAdminAuthenticated" />

    <InfoCard
      :responses="responses"
      @fileChange="onFileChange"
      v-if="isAdminAuthenticated"
    />
    <div
      v-if="uploadingFiles.length && isAdminAuthenticated"
      class="full-upload-card"
    >
      <div
        v-for="(file, index) in uploadingFiles"
        :key="index"
        class="upload-card"
      >
        <p class="">{{ file.name }}</p>
        <p class="upload-card-load">{{ file.status }}</p>
      </div>
    </div>
    <div v-if="responses.length > 0 && isAdminAuthenticated" class="card">
      <div
        v-for="(response, index) in responses"
        :key="index"
        class="response-card"
      >
        <div class="document-info">
          <h2>Тип документа: {{ response.type }}</h2>
          <table>
            <tbody v-if="response.type === 'passport'">
              <tr v-for="(value, key) in passportFields" :key="key">
                <td class="documentField">
                  <strong>{{ key }}:</strong>
                </td>
                <td>
                  <textarea
                    v-if="key === 'Кем выдан'"
                    v-model="response[value]"
                    class="documentValue"
                    rows="1"
                  ></textarea>
                  <input
                    v-else
                    v-model="response[value]"
                    class="documentValue"
                  />
                </td>
              </tr>
            </tbody>
            <tbody v-if="response.type === 'driver_license'">
              <tr v-for="(value, key) in driverLicenseFields" :key="key">
                <td class="documentField">
                  <strong>{{ key }}:</strong>
                </td>
                <td>
                  <input v-model="response[value]" class="documentValue" />
                </td>
              </tr>
            </tbody>
          </table>
        </div>
        <img
          :src="'data:image/jpeg;base64,' + response.image"
          alt="Документ"
          class="document-image"
          :class="response.type"
        />
        <button @click="deleteResponse(index)">Удалить</button>
        <!-- Кнопка для удаления данных -->
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import ApiComponent from "./components/ApiComponent.vue";
import Header from "./components/Header.vue";
import InfoCard from "./components/InfoCard.vue";
import RegisterFormAdmin from "./components/RegisterFormAdmin.vue";

export default {
  name: "App",
  components: {
    ApiComponent,
    Header,
    InfoCard,
    RegisterFormAdmin
  },
  data() {
    return {
      responses: [],
      uploadingFiles: [],
      isAdminAuthenticated: false,
      nameSource: "passport",
      passportFields: {
        ФИО: "name",
        Пол: "gender",
        "Дата рождения": "date-of-birth",
        Серия: "serial",
        Номер: "number",
        "Дата выдачи": "issue-date",
        "Кем выдан": "issued-by",
        "Код подразделения": "department-code",
      },
      driverLicenseFields: {
        Фамилия: "driver-license-1",
        "Имя и отчество": "driver-license-2",
        "Дата выдачи": "driver-license-4a",
        Серия: "driver-license-5-serial",
        Номер: "driver-license-5-number",
        Категории: "driver-license-9",
      },
    };
  },
  methods: {
    async onFileChange(e) {
      if (!this.isAdminAuthenticated) {
        return;
      }

      const files = Array.from(e.target.files);
      const newResponses = [];
      this.uploadingFiles = files.map((file) => ({
        name: file.name,
        status: "Загрузка...",
      }));

      for (let i = 0; i < files.length; i++) {
        const formData = new FormData();
        formData.append("file", files[i]);

        try {
          const apiUrl = import.meta.env.VITE_API_URL;
          const response = await fetch(apiUrl, {
            method: "POST",
            headers: {
              accept: "application/json",
            },
            body: formData,
          });

          if (!response.ok) {
            throw new Error("Ошибка HTTP: " + response.status);
          }

          const data = await response.json();
          newResponses.push(...data);
        } catch (error) {
          console.error("Ошибка:", error);
        }

        this.uploadingFiles[i].status = "Загружено";
      }

      this.responses.push(...newResponses);
    },
    deleteResponse(index) {
      this.responses.splice(index, 1);
    },
    handleLoginSuccess() {
      this.isAdminAuthenticated = true;
    },
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
          this.isAdminAuthenticated = true;
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

<style>
/* Ваши стили остаются без изменений */
</style>


<style>
* {
  max-width: 1200px;
  margin-left: auto;
  margin-right: auto;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #000000;
  margin-top: 60px;
  background-color: #ffffff;
}

.upload-card {
  margin-top: 10px;
  padding: 20px;
  border: 1px solid black;
  border-radius: 4px;
  display: flex;
  text-align: center;
  color: #000000;
  background-color: #ffffff;
}

.upload-card-image {
  text-align: left;
}

.card {
  width: 1200px;
}

.response-card {
  display: inline-flex;
  align-items: flex-start;
  width: 1160px;
  margin-top: 10px;
  padding: 20px;
  border: 1px solid black;
  border-radius: 4px;
  background-color: #ffffff;
}

.document-image {
  margin-left: 20px;
  width: 475px;
  margin-top: 33px;
  margin-right: 20px;
  border-radius: 3px;
  border: 1px solid #000000;
}

.document-info {
  width: 60%;
  text-align: left;
}

table {
  width: 100%;
  border-collapse: collapse;
}

td {
  padding: 10px;
}

.documentField {
  width: 165px;
  color: black;
}

.documentValue {
  border: 1px solid black;
  border-radius: 3px;
  padding: 5px;
  color: #000000;
}

input,
textarea {
  width: 100%;
  padding: 5px;
  box-sizing: border-box;
  border: 1px solid black;
  border-radius: 3px;
}

textarea {
  resize: none;
  overflow: hidden;
  height: 40px;
  line-height: 1.5;
}

button {
  background-color: black;
  color: #ffffff;
  border: none;
  border-radius: 3px;
  padding: 10px 20px;
  cursor: pointer;
}

button:hover {
  background-color: black;
}
</style>
