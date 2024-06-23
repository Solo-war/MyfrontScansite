<template>
  <div class="info-card">
    <div class="button">
      <label for="filePicker" class="filePickerLabel">
        Загрузить документы
        <input id="filePicker" class="filePicker" type="file" @change="onFileChange" accept="image/*" multiple />
      </label>
      <button class="exportExcel" @click="exportToExcel">Экспорт в Excel</button>
      <select v-model="nameSource" id="name-source" class="changedocument">
        <option value="passport">Паспорт</option>
        <option value="driver_license">Водительское удостоверение</option>
      </select>
    </div>
  </div>
</template>

<script>
import * as XLSX from 'xlsx';

export default {
  props: {
    responses: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      nameSource: 'passport',
    };
  },
  methods: {
    onFileChange(e) {
      this.$emit('fileChange', e);
    },
    exportToExcel() {
      const data = [];

      // Collect all responses and group them by unique ID (assuming ID exists and is unique per person)
      const responseMap = new Map();
      this.responses.forEach(response => {
        if (!responseMap.has(response.id)) {
          responseMap.set(response.id, {});
        }
        const personData = responseMap.get(response.id);
        personData[response.type] = response;
      });

      // Iterate over the responseMap to create rows for each person
      responseMap.forEach(personData => {
        let fullName = '';

        // Determine the full name based on the selected source
        if (this.nameSource === 'passport' && personData.passport) {
          fullName = personData.passport.name || '';
        } else if (this.nameSource === 'driver_license' && personData.driver_license) {
          const lastName = personData.driver_license['driver-license-1'] || '';
          const firstNameMiddleName = personData.driver_license['driver-license-2'] || '';
          fullName = `${lastName} ${firstNameMiddleName}`.trim();
        }

        const rowData = {
          ФИО: fullName,
          'День рождения': personData.passport ? personData.passport['date-of-birth'] || '' : '',
          'Серия паспорта': personData.passport ? personData.passport.serial || '' : '',
          'Номер паспорта': personData.passport ? personData.passport.number || '' : '',
          'Дата выдачи': personData.passport ? personData.passport['issue-date'] || '' : '',
          'Кем выдан': personData.passport ? personData.passport['issued-by'] || '' : '',
          'Код подразделения': personData.passport ? personData.passport['department-code'] || '' : '',
          'Серия ВУ': personData.driver_license ? personData.driver_license['driver-license-5-serial'] || '' : '',
          'Номер ВУ': personData.driver_license ? personData.driver_license['driver-license-5-number'] || '' : '',
          'Дата выдачи ВУ': personData.driver_license ? personData.driver_license['driver-license-4a'] || '' : '',
          Категории: personData.driver_license && personData.driver_license['driver-license-9']
            ? personData.driver_license['driver-license-9'].join(', ')
            : '',
        };
        data.push(rowData);
      });

      // Export to Excel
      const wb = XLSX.utils.book_new();
      const ws = XLSX.utils.json_to_sheet(data);
      XLSX.utils.book_append_sheet(wb, ws, 'Документы');
      XLSX.writeFile(wb, 'документы.xlsx');
    },
  },
};
</script>

<style scoped>

.info-card {
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
  border: 1px solid #ced4da;
  border-radius: 4px;
  background-color: white;
}

.button {
  display: flex;
  justify-content: space-between;
  width: 100%;

}

.filePicker {
  display: none;
}

.filePickerLabel {
  display: inline-block;
  width: 200px;
  height: 50px;
  background-color:  #3b5998 ;
  color: #fff;
  border: 3px solid  #3b5998 ;
  border-radius: 10px;
  cursor: pointer;
  font-size: 16px;
  font-weight: bold;
  line-height: 50px;
  text-align: center;
  vertical-align: middle;
}

.filePickerLabel:hover {
  background-color:  #2d4373 ;
    border: 3px solid  #3b5998 ;
  
}

.exportExcel {
  display: inline-block;
  width: 205px;
  height: 55px;
  background-color: #28a745;
  color: #fff;
  border: 3px solid #28a745;
  border-radius: 10px;
  cursor: pointer;
  font-size: 16px;
  font-weight: bold;
  text-align: center;
  vertical-align: middle;
}

.exportExcel:hover {
  background-color:  #218838;
}


.changedocument {
  border: 3px solid black;
  border-radius: 10px;
}
</style>

