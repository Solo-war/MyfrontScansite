<template>
  <div>
    <h1>Выберите фотографию</h1>
    <input
      className="button"
      type="file"
      @click="onFileChange"
      accept="image/*"
    />
    <input
      className="button"
      type="button"
      @click="uploadFile"
      value="Отправить фото"
    />

    <h2>Данные документа</h2>
    <ul>
      <li>
        <strong>Паспорт: </strong>
        <input type="text" placeholder="Тут должен быть " />
      </li>
      <li>
        <strong>Дата выдачи: </strong>
        <input type="text" placeholder="" />
      </li>
      <li>
        <strong>Код подразделения: </strong>
        <input type="text" placeholder="" />
      </li>
      <li>
        <strong>Фамилия: </strong>
        <input type="text" placeholder="" />
      </li>
      <li>
        <strong>Имя: </strong>
        <input type="text" placeholder="" />
      </li>
      <li>
        <strong>Отчество: </strong>
        <input type="text" placeholder="" />
      </li>
      <li>
        <strong>Пол: </strong>
        <input type="text" placeholder="" />
      </li>
      <li>
        <strong>Дата рождения: </strong>
        <input type="text" placeholder="" />
      </li>
      <li>
        <strong>Место рождения: </strong>
        <input type="text" placeholder="" />
      </li>
      <li>
        <strong>Серия и номер: </strong>
        <input type="text" placeholder="" />
      </li>
    </ul>

    <h2>Фотография</h2>
    <img src="" alt="Error" />
  </div>
</template>

<script>
</script>


 <style>
</style>