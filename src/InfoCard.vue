<template>
  <div v-if="responses.length > 0">
    <div v-for="(response, index) in responses" :key="index">
      <h2>Тип документа: {{ response.type }}</h2>
      <img :src="'data:image/jpeg;base64,' + response.image" alt="Документ" />

      <div v-if="response.type === 'passport'">
        <p><strong>Фамилия и имя:</strong> {{ response.name }}</p>
        <p><strong>Пол:</strong> {{ response.gender }}</p>
        <p><strong>Дата рождения:</strong> {{ response["date-of-birth"] }}</p>
        <p><strong>Серия:</strong> {{ response.serial }}</p>
        <p><strong>Номер:</strong> {{ response.number }}</p>
        <p><strong>Дата выдачи:</strong> {{ response["issue-date"] }}</p>
        <p><strong>Кем выдан:</strong> {{ response["issued-by"] }}</p>
        <p>
          <strong>Код подразделения:</strong>
          {{ response["department-code"] }}
        </p>
      </div>

      <div v-if="response.type === 'driver_license'">
        <p><strong>Фамилия:</strong> {{ response["driver-license-1"] }}</p>
        <p>
          <strong>Имя и отчество:</strong> {{ response["driver-license-2"] }}
        </p>
        <p>
          <strong>Дата рождения:</strong> {{ response["driver-license-3"] }}
        </p>
        <p><strong>Дата выдачи:</strong> {{ response["driver-license-4a"] }}</p>
        <p>
          <strong>Дата окончания:</strong> {{ response["driver-license-4b"] }}
        </p>
        <p><strong>Кем выдан:</strong> {{ response["driver-license-4c"] }}</p>
        <p><strong>Серия:</strong> {{ response["driver-license-5-serial"] }}</p>
        <p><strong>Номер:</strong> {{ response["driver-license-5-number"] }}</p>
        <p><strong>Место выдачи:</strong> {{ response["driver-license-8"] }}</p>
        <p>
          <strong>Категории:</strong>
          {{ response["driver-license-9"].join(", ") }}
        </p>
      </div>
    </div>
  </div>
</template>

<script>

</script>
