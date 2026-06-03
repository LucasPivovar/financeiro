<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import { Mail, Lock, LogIn } from '@lucide/vue';

const router = useRouter();
const email = ref('');
const password = ref('');
const isLoading = ref(false);

const handleLogin = () => {
  isLoading.value = true;
  setTimeout(() => {
    isLoading.value = false;
    router.push('/2fa');
  }, 1000);
};
</script>

<template>
  <div class="auth-card card">
    <div class="auth-header">
      <div class="logo-icon">
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M12 2L2 7L12 12L22 7L12 2Z" fill="white"/>
          <path d="M2 17L12 22L22 17" stroke="white" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
          <path d="M2 12L12 17L22 12" stroke="white" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </div>
      <h2>Bem-vindo de volta</h2>
      <p>Faça login para acessar o painel</p>
    </div>

    <form class="auth-form" @submit.prevent="handleLogin">
      <div class="form-group">
        <label>E-mail</label>
        <div class="input-icon-wrapper">
          <Mail size="18" class="input-icon" />
          <input type="email" v-model="email" placeholder="seu@email.com" required class="form-input with-icon" />
        </div>
      </div>
      
      <div class="form-group">
        <div class="label-row">
          <label>Senha</label>
          <a href="#" class="forgot-link">Esqueci a senha</a>
        </div>
        <div class="input-icon-wrapper">
          <Lock size="18" class="input-icon" />
          <input type="password" v-model="password" placeholder="••••••••" required class="form-input with-icon" />
        </div>
      </div>

      <button type="submit" class="btn-primary w-full" :disabled="isLoading">
        <span v-if="isLoading">Entrando...</span>
        <span v-else class="flex-center">
          Entrar <LogIn size="18" />
        </span>
      </button>
    </form>

    <div class="auth-footer">
      <p>Não tem uma conta? <router-link to="/register" class="auth-link">Criar conta</router-link></p>
    </div>
  </div>
</template>

<style scoped>
.auth-card {
  width: 100%;
  max-width: 400px;
  padding: 40px;
  display: flex;
  flex-direction: column;
  gap: 32px;
}

.auth-header {
  text-align: center;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 12px;
}

.logo-icon {
  width: 48px;
  height: 48px;
  background-color: var(--primary);
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-bottom: 8px;
}

.auth-header h2 {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--text-main);
}

.auth-header p {
  color: var(--text-muted);
  font-size: 0.95rem;
}

.auth-form {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.label-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.form-group label {
  font-size: 0.85rem;
  font-weight: 500;
  color: var(--text-main);
}

.forgot-link {
  font-size: 0.8rem;
  color: var(--primary);
  font-weight: 500;
}

.forgot-link:hover {
  text-decoration: underline;
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

.w-full {
  width: 100%;
  justify-content: center;
  padding: 12px;
  font-size: 1rem;
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
