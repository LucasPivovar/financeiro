<script setup>
import { Bell } from '@lucide/vue';
</script>

<template>
  <header class="topbar">
    <div class="topbar-inner">
      <div class="spacer"></div>
      
      <!-- Right Actions -->
      <div class="actions">
        <button class="icon-btn notification-btn">
          <Bell size="20" />
          <span class="notification-dot">3</span>
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
</style>
