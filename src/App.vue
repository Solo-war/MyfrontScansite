<template>
  <div id="app">
    <Header />
    <h1>Загрузите фотографию документа</h1>
    <input type="file" @change="onFileChange" accept="image/*" multiple />
    <div v-if="responses.length > 0">
      <div v-for="(response, index) in responses" :key="index">
        <h2>Тип документа: {{ response.type }}</h2>
        <img :src="'data:image/jpeg;base64,' + response.image" alt="Документ" />
        <table>
          <tbody v-if="response.type === 'passport'">
            <tr>
              <td><strong>Фамилия и имя:</strong></td>
              <td><input v-model="response.name" /></td>
            </tr>
            <tr>
              <td><strong>Пол:</strong></td>
              <td><input v-model="response.gender" /></td>
            </tr>
            <tr>
              <td><strong>Дата рождения:</strong></td>
              <td><input v-model="response['date-of-birth']" /></td>
            </tr>
            <tr>
              <td><strong>Серия:</strong></td>
              <td><input v-model="response.serial" /></td>
            </tr>
            <tr>
              <td><strong>Номер:</strong></td>
              <td><input v-model="response.number" /></td>
            </tr>
            <tr>
              <td><strong>Дата выдачи:</strong></td>
              <td><input v-model="response['issue-date']" /></td>
            </tr>
            <tr>
              <td><strong>Кем выдан:</strong></td>
              <td><input v-model="response['issued-by']" /></td>
            </tr>
            <tr>
              <td><strong>Код подразделения:</strong></td>
              <td><input v-model="response['department-code']" /></td>
            </tr>
          </tbody>
          <tbody v-if="response.type === 'driver_license'">
            <tr>
              <td><strong>Фамилия:</strong></td>
              <td><input v-model="response['driver-license-1']" /></td>
            </tr>
            <tr>
              <td><strong>Имя и отчество:</strong></td>
              <td><input v-model="response['driver-license-2']" /></td>
            </tr>
            <tr>
              <td><strong>Дата рождения:</strong></td>
              <td><input v-model="response['driver-license-3']" /></td>
            </tr>
            <tr>
              <td><strong>Дата выдачи:</strong></td>
              <td><input v-model="response['driver-license-4a']" /></td>
            </tr>
            <tr>
              <td><strong>Дата окончания:</strong></td>
              <td><input v-model="response['driver-license-4b']" /></td>
            </tr>
            <tr>
              <td><strong>Кем выдан:</strong></td>
              <td><input v-model="response['driver-license-4c']" /></td>
            </tr>
            <tr>
              <td><strong>Серия:</strong></td>
              <td><input v-model="response['driver-license-5-serial']" /></td>
            </tr>
            <tr>
              <td><strong>Номер:</strong></td>
              <td><input v-model="response['driver-license-5-number']" /></td>
            </tr>
            <tr>
              <td><strong>Место выдачи:</strong></td>
              <td><input v-model="response['driver-license-8']" /></td>
            </tr>
            <tr>
              <td><strong>Категории:</strong></td>
              <td><input v-model="response['driver-license-9']" /></td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
    <ExcelFile :responses="responses" />
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import ExcelFile from "./components/ExcelFile.vue";

export default {
  name: "App",
  components: {
    Header,
    ExcelFile,
  },
  data() {
    return {
      responses: [],
    };
  },
  methods: {
    async onFileChange(e) {
      const files = e.target.files;
      const newResponses = [];

      for (let i = 0; i < files.length; i++) {
        const formData = new FormData();
        formData.append("file", files[i]);

        try {
          const apiUrl = import.meta.env.VITE_API_URL; // Используем правильную переменную окружения
          console.log(apiUrl);
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
          newResponses.push(...data); // Предполагаем, что API возвращает массив объектов
        } catch (error) {
          console.error("Ошибка:", error);
        }
      }

      this.responses.push(...newResponses);
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
}

img {
  width: 10%;
  height: 10%;
}
</style>
