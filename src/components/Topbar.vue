<script setup>
import { ref, inject, computed } from 'vue';
import { Bell, Menu, FileText, CheckCircle, AlertTriangle } from '@lucide/vue';
import Modal from './Modal.vue';

const toggleMenu = inject('toggleMobileMenu');

const showNotifications = ref(false);
const notifications = [
  { id: 1, type: 'success', title: 'Proposta Aprovada', text: 'A proposta #1245 para Carlos Santos foi aprovada pelo banco.', time: 'Há 10 min', icon: CheckCircle, color: 'green' },
  { id: 2, type: 'warning', title: 'Documento Pendente', text: 'Falta enviar o CNH da cliente Maria Oliveira.', time: 'Há 2 horas', icon: AlertTriangle, color: 'yellow' },
  { id: 3, type: 'info', title: 'Nova Lead Recebida', text: 'João Silva demonstrou interesse no Toyota Corolla.', time: 'Ontem', icon: FileText, color: 'blue' }
];

const unreadCount = computed(() => notifications.length);
</script>

<template>
  <header class="topbar">
    <div class="topbar-inner">
      <button class="mobile-menu-btn icon-btn" @click="toggleMenu">
        <Menu size="24" />
      </button>
      <div class="spacer"></div>
      
      <!-- Right Actions -->
      <div class="actions">
        <button class="icon-btn notification-btn" @click="showNotifications = true">
          <Bell size="20" />
          <span v-if="unreadCount > 0" class="notification-dot">{{ unreadCount }}</span>
        </button>
        
        <div class="user-profile">
          <div class="user-info">
            <span class="user-name">Auto Motors</span>
            <span class="user-role">Lojista</span>
          </div>
          <div class="user-avatar">
            AM
          </div>
        </div>
      </div>
    </div>
  </header>

  <!-- Modal de Notificações -->
  <Modal :show="showNotifications" title="Notificações" @close="showNotifications = false">
    <div class="notifications-list">
      <div v-for="notif in notifications" :key="notif.id" class="notification-item">
        <div class="notification-icon" :class="notif.color">
          <component :is="notif.icon" size="18" />
        </div>
        <div class="notification-content">
          <div class="notification-header">
            <h4>{{ notif.title }}</h4>
            <span class="notification-time">{{ notif.time }}</span>
          </div>
          <p>{{ notif.text }}</p>
        </div>
      </div>
      <div v-if="notifications.length === 0" class="empty-state">
        <p>Você não tem novas notificações.</p>
      </div>
    </div>
    <div style="display: flex; justify-content: flex-end; margin-top: 16px; border-top: 1px solid var(--border); padding-top: 16px;">
      <button class="btn-primary" @click="showNotifications = false">Marcar todas como lidas</button>
    </div>
  </Modal>
</template>

<style scoped>
.topbar {
  background-color: var(--surface);
  border-bottom: 1px solid var(--border);
  position: sticky;
  top: 0;
  z-index: 90;
}

.topbar-inner {
  padding: 0 32px;
  height: 72px;
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.spacer {
  flex: 1;
}

.mobile-menu-btn {
  display: none !important;
  margin-right: 16px;
}

@media (max-width: 1024px) {
  .mobile-menu-btn {
    display: flex !important;
  }
}

.actions {
  display: flex;
  align-items: center;
  gap: 24px;
}

.icon-btn {
  color: var(--text-muted);
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
  width: 40px;
  height: 40px;
  border-radius: 50%;
}

.icon-btn:hover {
  background-color: var(--bg-color);
  color: var(--text-main);
}

.notification-dot {
  position: absolute;
  top: 4px;
  right: 4px;
  background-color: var(--danger);
  color: white;
  font-size: 0.65rem;
  font-weight: bold;
  width: 16px;
  height: 16px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  border: 2px solid var(--surface);
}

.user-profile {
  display: flex;
  align-items: center;
  gap: 12px;
  cursor: pointer;
  padding: 4px 8px;
  border-radius: var(--radius-md);
  transition: background-color 0.2s;
}

.user-profile:hover {
  background-color: var(--bg-color);
}

.user-info {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}

.user-name {
  font-size: 0.9rem;
  font-weight: 600;
  color: var(--text-main);
}

.user-role {
  font-size: 0.75rem;
  color: var(--text-muted);
}

.user-avatar {
  width: 40px;
  height: 40px;
  background-color: var(--primary);
  color: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 600;
  font-size: 0.9rem;
}

/* Notifications Modal Styles */
.notifications-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.notification-item {
  display: flex;
  gap: 16px;
  padding: 12px;
  border-radius: var(--radius-lg);
  background-color: var(--bg-color);
  transition: background-color 0.2s;
}

.notification-item:hover {
  background-color: var(--surface);
}

.notification-icon {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.notification-icon.green { background-color: #DCFCE7; color: #16A34A; }
.notification-icon.yellow { background-color: #FEF9C3; color: #CA8A04; }
.notification-icon.blue { background-color: #DBEAFE; color: #2563EB; }

.notification-content {
  flex: 1;
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.notification-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.notification-header h4 {
  font-size: 0.9rem;
  font-weight: 600;
  color: var(--text-main);
  margin: 0;
}

.notification-time {
  font-size: 0.75rem;
  color: var(--text-muted);
}

.notification-content p {
  font-size: 0.85rem;
  color: var(--text-muted);
  margin: 0;
  line-height: 1.4;
}

.empty-state {
  padding: 32px;
  text-align: center;
  color: var(--text-muted);
}
</style>
