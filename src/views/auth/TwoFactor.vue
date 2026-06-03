<script setup>
import { ref } from 'vue';
import { useRouter } from 'vue-router';
import { ShieldCheck, ArrowRight } from '@lucide/vue';

const router = useRouter();
const code = ref(['', '', '', '', '', '']);
const inputs = ref([]);
const isLoading = ref(false);

const handleInput = (e, index) => {
  const value = e.target.value;
  
  if (value && index < 5) {
    inputs.value[index + 1].focus();
  }
};

const handleKeydown = (e, index) => {
  if (e.key === 'Backspace' && !code.value[index] && index > 0) {
    inputs.value[index - 1].focus();
  }
};

const verifyCode = () => {
  const fullCode = code.value.join('');
  if (fullCode.length !== 6) return;

  isLoading.value = true;
  setTimeout(() => {
    isLoading.value = false;
    router.push('/');
  }, 1000);
};
</script>

<template>
  <div class="auth-card card">
    <div class="auth-header">
      <div class="shield-icon">
        <ShieldCheck size="28" />
      </div>
      <h2>Verificação em Duas Etapas</h2>
      <p>Digite o código de 6 dígitos enviado para o seu dispositivo.</p>
    </div>

    <form class="auth-form" @submit.prevent="verifyCode">
      <div class="code-inputs">
        <input 
          v-for="(_, index) in 6" 
          :key="index"
          type="text" 
          maxlength="1" 
          v-model="code[index]"
          @input="handleInput($event, index)"
          @keydown="handleKeydown($event, index)"
          ref="inputs"
          class="code-digit"
        />
      </div>

      <button type="submit" class="btn-primary w-full" :disabled="isLoading || code.join('').length < 6">
        <span v-if="isLoading">Verificando...</span>
        <span v-else class="flex-center">
          Confirmar e Acessar <ArrowRight size="18" />
        </span>
      </button>
    </form>

    <div class="auth-footer">
      <p>Não recebeu o código? <a href="#" class="auth-link">Reenviar</a></p>
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

.shield-icon {
  width: 56px;
  height: 56px;
  background-color: var(--primary-light);
  color: var(--primary);
  border-radius: 50%;
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
  line-height: 1.4;
}

.auth-form {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.code-inputs {
  display: flex;
  justify-content: space-between;
  gap: 8px;
}

.code-digit {
  width: 48px;
  height: 56px;
  border: 1px solid var(--border);
  border-radius: var(--radius-md);
  text-align: center;
  font-size: 1.5rem;
  font-weight: 600;
  font-family: var(--font-family);
  color: var(--text-main);
  outline: none;
  transition: all 0.2s;
  background-color: white;
}

.code-digit:focus {
  border-color: var(--primary);
  box-shadow: 0 0 0 3px var(--primary-light);
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
