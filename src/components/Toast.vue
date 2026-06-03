<script setup>
import { CheckCircle, XCircle, AlertCircle, Info, X } from '@lucide/vue';
import { useToast } from '../composables/useToast';

const { toasts, removeToast } = useToast();

const getIcon = (type) => {
  switch (type) {
    case 'success': return CheckCircle;
    case 'error': return XCircle;
    case 'warning': return AlertCircle;
    case 'info': return Info;
    default: return Info;
  }
};
</script>

<template>
  <div class="toast-container">
    <TransitionGroup name="toast">
      <div 
        v-for="toast in toasts" 
        :key="toast.id" 
        class="toast"
        :class="`toast-${toast.type}`"
      >
        <div class="toast-icon">
          <component :is="getIcon(toast.type)" size="20" />
        </div>
        <div class="toast-message">
          {{ toast.message }}
        </div>
        <button class="toast-close" @click="removeToast(toast.id)">
          <X size="16" />
        </button>
      </div>
    </TransitionGroup>
  </div>
</template>

<style scoped>
.toast-container {
  position: fixed;
  bottom: 24px;
  right: 24px;
  display: flex;
  flex-direction: column;
  gap: 12px;
  z-index: 9999;
}

.toast {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 16px 20px;
  background-color: white;
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow-md);
  min-width: 300px;
  border-left: 4px solid;
}

.toast-success { border-color: var(--success); }
.toast-success .toast-icon { color: var(--success); }

.toast-error { border-color: var(--danger); }
.toast-error .toast-icon { color: var(--danger); }

.toast-warning { border-color: var(--warning); }
.toast-warning .toast-icon { color: var(--warning); }

.toast-info { border-color: var(--info); }
.toast-info .toast-icon { color: var(--info); }

.toast-message {
  flex: 1;
  font-size: 0.95rem;
  font-weight: 500;
  color: var(--text-main);
}

.toast-close {
  color: var(--text-muted);
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 4px;
  border-radius: 4px;
}

.toast-close:hover {
  background-color: var(--bg-color);
  color: var(--text-main);
}

/* Transitions */
.toast-enter-active,
.toast-leave-active {
  transition: all 0.3s ease;
}

.toast-enter-from {
  opacity: 0;
  transform: translateX(100%);
}

.toast-leave-to {
  opacity: 0;
  transform: translateX(100%);
}
</style>
