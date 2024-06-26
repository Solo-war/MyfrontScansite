<template>
  <div>
    <button @click="toggleForm">{{ isAdminForm ? 'Вход для User' : 'Вход для Admin' }}</button>
    <RegisterFormAdmin 
      v-if="isAdminForm" 
      @switch-form="toggleForm" 
      @authenticated="handleAdminAuthenticated" 
    />
    <RegisterFormUser 
      v-else 
      @switch-form="toggleForm" 
      @authenticated="handleUserAuthenticated" 
    />
  </div>
</template>

<script>
import RegisterFormAdmin from './RegisterFormAdmin.vue';
import RegisterFormUser from './RegisterFormUser.vue';

export default {
  components: {
    RegisterFormAdmin,
    RegisterFormUser,
  },
  data() {
    return {
      isAdminForm: false,
    };
  },
  methods: {
    toggleForm() {
      this.isAdminForm = !this.isAdminForm;
    },
    handleAdminAuthenticated() {
      this.$emit('login-success');
    },
    handleUserAuthenticated() {
      // Логика аутентификации для пользователя
      this.$emit('login-success');
    }
  },
};
</script>
