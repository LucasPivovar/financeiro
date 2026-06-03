<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import { User, Mail, Phone, Lock, UserPlus } from '@lucide/vue';

const router = useRouter();

const form = ref({
  name: '',
  email: '',
  phone: '',
  password: '',
  confirmPassword: ''
});

const errors = ref({});
const isLoading = ref(false);

const validatePhone = (phone) => {
  const digits = phone.replace(/\D/g, '');
  return digits.length >= 10 && digits.length <= 11;
};

const validateEmail = (email) => {
  return /^[^\s@]+@[^\s@]+\.[^\s@]+$/.test(email);
};

const handleRegister = () => {
  errors.value = {};
  
  if (!form.value.name) errors.value.name = 'Nome é obrigatório';
  if (!validateEmail(form.value.email)) errors.value.email = 'E-mail inválido';
  if (!validatePhone(form.value.phone)) errors.value.phone = 'Telefone inválido (mínimo 10 dígitos)';
  if (form.value.password.length < 6) errors.value.password = 'Senha deve ter no mínimo 6 caracteres';
  if (form.value.password !== form.value.confirmPassword) errors.value.confirmPassword = 'As senhas não coincidem';

  if (Object.keys(errors.value).length > 0) return;

  isLoading.value = true;
  setTimeout(() => {
    isLoading.value = false;
    router.push('/login');
  }, 1200);
};

const applyPhoneMask = (e) => {
  let value = e.target.value.replace(/\D/g, '');
  if (value.length > 11) value = value.slice(0, 11);
  
  if (value.length > 2) {
    value = `(${value.slice(0, 2)}) ${value.slice(2)}`;
  }
  if (value.length > 9) {
    value = `${value.slice(0, 10)}-${value.slice(10)}`;
  }
  form.value.phone = value;
};
</script>

<template>
  <div class="auth-card card">
    <div class="auth-header">
      <h2>Criar Conta</h2>
      <p>Junte-se à Start Contempladas</p>
    </div>

    <form class="auth-form" @submit.prevent="handleRegister">
      <div class="form-group" :class="{ 'has-error': errors.name }">
        <label>Nome Completo</label>
        <div class="input-icon-wrapper">
          <User size="18" class="input-icon" />
          <input type="text" v-model="form.name" placeholder="Ex: João da Silva" class="form-input with-icon" />
        </div>
        <span class="error-msg" v-if="errors.name">{{ errors.name }}</span>
      </div>

      <div class="form-group" :class="{ 'has-error': errors.email }">
        <label>E-mail</label>
        <div class="input-icon-wrapper">
          <Mail size="18" class="input-icon" />
          <input type="email" v-model="form.email" placeholder="joao@email.com" class="form-input with-icon" />
        </div>
        <span class="error-msg" v-if="errors.email">{{ errors.email }}</span>
      </div>

      <div class="form-group" :class="{ 'has-error': errors.phone }">
        <label>Celular</label>
        <div class="input-icon-wrapper">
          <Phone size="18" class="input-icon" />
          <input type="text" v-model="form.phone" @input="applyPhoneMask" placeholder="(11) 99999-9999" class="form-input with-icon" />
        </div>
        <span class="error-msg" v-if="errors.phone">{{ errors.phone }}</span>
      </div>
      
      <div class="form-group" :class="{ 'has-error': errors.password }">
        <label>Senha</label>
        <div class="input-icon-wrapper">
          <Lock size="18" class="input-icon" />
          <input type="password" v-model="form.password" placeholder="••••••••" class="form-input with-icon" />
        </div>
        <span class="error-msg" v-if="errors.password">{{ errors.password }}</span>
      </div>

      <div class="form-group" :class="{ 'has-error': errors.confirmPassword }">
        <label>Confirmar Senha</label>
        <div class="input-icon-wrapper">
          <Lock size="18" class="input-icon" />
          <input type="password" v-model="form.confirmPassword" placeholder="••••••••" class="form-input with-icon" />
        </div>
        <span class="error-msg" v-if="errors.confirmPassword">{{ errors.confirmPassword }}</span>
      </div>

      <button type="submit" class="btn-primary w-full" :disabled="isLoading">
        <span v-if="isLoading">Criando conta...</span>
        <span v-else class="flex-center">
          Cadastrar <UserPlus size="18" />
        </span>
      </button>
    </form>

    <div class="auth-footer">
      <p>Já possui uma conta? <router-link to="/login" class="auth-link">Faça login</router-link></p>
    </div>
  </div>
</template>

<style scoped>
.auth-card {
  width: 100%;
  max-width: 480px;
  padding: 40px;
  display: flex;
  flex-direction: column;
  gap: 32px;
  margin: 40px 0;
}

.auth-header {
  text-align: center;
}

.auth-header h2 {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--text-main);
}

.auth-header p {
  color: var(--text-muted);
  font-size: 0.95rem;
  margin-top: 4px;
}

.auth-form {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 6px;
}

.form-group label {
  font-size: 0.85rem;
  font-weight: 500;
  color: var(--text-main);
}

.input-icon-wrapper {
  position: relative;
}

.input-icon {
  position: absolute;
  left: 12px;
  top: 50%;
  transform: translateY(-50%);
  color: var(--text-muted);
}

.form-input {
  width: 100%;
  padding: 12px 16px;
  border: 1px solid var(--border);
  border-radius: var(--radius-md);
  font-family: var(--font-family);
  font-size: 0.95rem;
  outline: none;
  transition: all 0.2s;
}

.form-input:focus {
  border-color: var(--primary);
  box-shadow: 0 0 0 3px var(--primary-light);
}

.form-input.with-icon {
  padding-left: 40px;
}

.has-error .form-input {
  border-color: var(--danger);
}

.has-error .form-input:focus {
  box-shadow: 0 0 0 3px var(--danger-bg);
}

.has-error .input-icon {
  color: var(--danger);
}

.error-msg {
  font-size: 0.75rem;
  color: var(--danger);
  font-weight: 500;
}

.w-full {
  width: 100%;
  justify-content: center;
  padding: 12px;
  font-size: 1rem;
  margin-top: 8px;
}

.flex-center {
  display: flex;
  align-items: center;
  gap: 8px;
}

.auth-footer {
  text-align: center;
  font-size: 0.9rem;
  color: var(--text-muted);
}

.auth-link {
  color: var(--primary);
  font-weight: 600;
}

.auth-link:hover {
  text-decoration: underline;
}
</style>
