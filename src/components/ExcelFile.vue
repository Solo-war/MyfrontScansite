<template>
  <div>

  </div>
</template>

<script>
import * as XLSX from "xlsx"; // Import all exports from xlsx as XLSX

export default {
  props: {
    responses: {
      type: Array,
      required: true,
    },
  },
  methods: {
    exportToExcel() {
      // Подготовка данных для экспорта
      const data = [];

      this.responses.forEach((response) => {
        if (response.type === "passport") {
          const rowData = {
            ФИО: response.name || "",
            "День рождения": response["date-of-birth"] || "",
            "Серия паспорта": response.serial || "",
            "Номер паспорта": response.number || "",
            "Дата выдачи": response["issue-date"] || "",
            "Кем выдан": response["issued-by"] || "",
            "код подразделения": response["department-code"] || "",
            "серия ву": "",
            "номер ву": "",
            "дата выдачи ву": "",
            категории: "",
          };
          data.push(rowData);
        } else if (response.type === "driver_license") {
          const rowData = {
            ФИО:
              `${response["driver-license-1"]} ${response["driver-license-2"]}` ||
              "",
            "День рождения": response["driver-license-3"] || "",
            "Серия паспорта": "",
            "Номер паспорта": "",
            "Дата выдачи": response["driver-license-4a"] || "",
            "Кем выдан": response["driver-license-4c"] || "",
            "код подразделения": "",
            "серия ву": response["driver-license-5-serial"] || "",
            "номер ву": response["driver-license-5-number"] || "",
            "дата выдачи ву": response["driver-license-4a"] || "",
            категории: response["driver-license-9"]
              ? response["driver-license-9"].join(", ")
              : "",
          };
          data.push(rowData);
        }
      });

      // Создание новой книги Excel
      const wb = XLSX.utils.book_new();
      const ws = XLSX.utils.json_to_sheet(data);

      // Добавление листа с данными в книгу
      XLSX.utils.book_append_sheet(wb, ws, "Документы");

      // Сохранение книги как файла Excel
      XLSX.writeFile(wb, "документы.xlsx");
    },
  },
};
</script>

<style scoped>
button {
  width: 200px;
  height: 50px;
  background-color: #132;
  color: #fff ;
  border: 3px solid #6543;
  border-radius: 10px;

  
}
</style>
