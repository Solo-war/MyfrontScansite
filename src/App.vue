<template>
  <div id="app">
    <AdminPanel
      v-if="isAuthenticated && isRootUser"
      :isAuthenticated="isAuthenticated"
      :isRootUser="isRootUser"
      @logout="handleLogout"
    />
    <RegisterSwitch
      v-if="!isAuthenticated"
      @loginsuccess="handleLoginSuccess"
    />
    <Header v-if="isAuthenticated && !isRootUser" />

    <InfoCard
      :responses="responses"
      @fileChange="onFileChange"
      @newResponses="addResponses"
      v-if="isAuthenticated && !isRootUser"
    />

    <div
      v-if="uploadingFiles.length && isAuthenticated"
      class="full-upload-card"
    >
      <div
        v-for="(file, index) in uploadingFiles"
        :key="index"
        class="upload-card"
      >
        <p>{{ file.name }}</p>
        <p class="upload-card-load">{{ file.status }}</p>
      </div>
    </div>

    <div v-if="responses.length > 0 && isAuthenticated" class="card">
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
        <template v-if="response.image">
          <img
            :src="'data:image/jpeg;base64,' + response.image"
            alt="Документ"
            class="document-image"
            :class="response.type"
            @click="downloadImage(response)"
          />
        </template>
        <template v-else>
          <p>Изображение не загружено</p>
        </template>
        <button @click="deleteResponse(index)" class="trash">❌</button>
      </div>
    </div>
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import InfoCard from "./components/InfoCard.vue";
import adminPanel from "./components/adminPanel.vue";
import RegisterSwitch from "./components/RegisterSwitch.vue";

export default {
  name: "App",
  components: {
    Header,
    InfoCard,
    AdminPanel,
    RegisterSwitch,
  },
  data() {
    return {
      responses: [],
      uploadingFiles: [],
      isAuthenticated: false,
      isRootUser: false,
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
    handleLoginSuccess(user) {
      console.log(user);
      this.isAuthenticated = true;
      this.isRootUser = user.user.login == "root";
    },

    downloadImage(response) {
      const imageData = response.image;
      if (!imageData) {
        alert("Изображение отсутствует");
        return;
      }

      const link = document.createElement("a");
      if (response.type === "driver_license") {
        link.download =
          response["driver-license-1"] + response["driver-license-2"] + ".jpg";
      }
      if (response.type === "passport") {
        link.download = response.name + ".jpg";
      }
      link.href = "data:image/jpeg;base64," + imageData;

      link.click();
    },

    async onFileChange(e) {
      if (!this.isAuthenticated) {
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

          if (!apiUrl) {
            throw new Error("API URL не задан. Проверьте настройки.");
          }

          const response = await fetch(apiUrl, {
            method: "POST",
            headers: {
              accept: "application/json",
            },
            body: formData,
          });

          if (!response.ok) {
            throw new Error(`Ошибка HTTP: ${response.status}`);
          }

          const data = await response.json();
          newResponses.push(...data);
          this.uploadingFiles[i].status = "Загружено";
        } catch (error) {
          console.error(`Ошибка при загрузке файла: ${files[i].name}`, error);
          this.uploadingFiles[i].status = `Ошибка: ${error.message}`;
        }
      }

      this.addResponses(newResponses);
    },

    addResponses(newResponses) {
      this.responses = this.responses.concat(newResponses);
      this.uploadingFiles = [];
    },

    deleteResponse(index) {
      this.responses.splice(index, 1);
    },
    handleLogout() {
      this.isAuthenticated = false;
      this.isRootUser = false;
    },
  },
};
</script>

<style >
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
</style>
<style scoped>
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

.info-card {
  border: 1px solid black;
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

.upload-wrapper {
  position: relative;
  display: inline-block;
}

.upload-wrapper img {
  display: block;
}

.upload-wrapper::after {
  content: "Скачать";
  position: absolute;
  bottom: 10px;
  left: 50%;
  transform: translateX(-50%);
  padding: 5px 10px;
  background: rgba(0, 0, 0, 0.5);
  color: #fff;
  border-radius: 5px;
  opacity: 0;
  transition: opacity 0.3s ease;
  pointer-events: none; /* Prevents interaction with the pseudo-element */
}

.upload-wrapper:hover::after {
  opacity: 1;
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
  overflow-y: scroll;
  margin-bottom: 20px;  
  height: 50px;
  line-height: 1.5;
  resize: none;
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
  background-color: rgb(0, 0, 0);
}

.trash {
  position: relative;
  right: 10px;
  font-size: 16px;
  width: 20px;
  background-color: rgba(0, 0, 0, 0);
}

.trash:hover {
  background-color: white;
}
</style>
