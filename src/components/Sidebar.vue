<script setup>
import { 
  LayoutDashboard, 
  Car, 
  FileText, 
  Users, 
  DollarSign, 
  BarChart2, 
  Settings,
  LogOut
} from '@lucide/vue';
import { useRoute, useRouter } from 'vue-router';

const route = useRoute();
const router = useRouter();

const handleLogout = () => {
  router.push('/login');
};

const navItems = [
  { name: 'Visão Geral', path: '/', icon: LayoutDashboard },
  { name: 'Estoque', path: '/inventory', icon: Car },
  { name: 'Propostas', path: '/proposals', icon: FileText },
  { name: 'Clientes', path: '/clients', icon: Users },
  { name: 'Comissões', path: '/commissions', icon: DollarSign },
  { name: 'Relatórios', path: '/reports', icon: BarChart2 },
  { name: 'Configurações', path: '/settings', icon: Settings },
];
</script>

<template>
  <aside class="sidebar">
    <div class="logo-container">
      <div class="logo-icon">
        <svg width="24" height="24" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
          <path d="M12 2L2 7L12 12L22 7L12 2Z" fill="white"/>
          <path d="M2 17L12 22L22 17" stroke="white" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
          <path d="M2 12L12 17L22 12" stroke="white" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/>
        </svg>
      </div>
      <div class="logo-text">
        <span class="brand">START</span>
        <span class="sub">CONTEMPLADAS</span>
      </div>
    </div>

    <nav class="nav-menu">
      <router-link 
        v-for="item in navItems" 
        :key="item.path"
        :to="item.path"
        class="nav-item"
        :class="{ active: route.path === item.path }"
      >
        <component :is="item.icon" size="20" class="nav-icon" />
        <span class="nav-text">{{ item.name }}</span>
      </router-link>
    </nav>

    <div class="sidebar-footer">
      <button class="nav-item logout-btn" @click="handleLogout">
        <LogOut size="20" class="nav-icon" />
        <span class="nav-text">Sair da Conta</span>
      </button>
    </div>
  </aside>
</template>

<style scoped>
.sidebar {
  width: 260px;
  background-color: var(--surface);
  border-right: 1px solid var(--border);
  display: flex;
  flex-direction: column;
  height: 100vh;
  position: sticky;
  top: 0;
  z-index: 100;
}

.logo-container {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 24px;
  border-bottom: 1px solid var(--border);
  margin-bottom: 16px;
}

.logo-icon {
  width: 32px;
  height: 32px;
  background-color: var(--primary);
  border-radius: 8px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.logo-text {
  display: flex;
  flex-direction: column;
}

.brand {
  font-weight: 700;
  font-size: 1.2rem;
  line-height: 1.1;
  color: #1A1C23;
  letter-spacing: -0.5px;
}

.sub {
  font-size: 0.65rem;
  font-weight: 600;
  color: var(--text-muted);
  letter-spacing: 0.5px;
}

.nav-menu {
  display: flex;
  flex-direction: column;
  gap: 4px;
  padding: 0 16px;
  flex: 1;
  overflow-y: auto;
}

.nav-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px 16px;
  border-radius: var(--radius-lg);
  color: var(--text-muted);
  font-weight: 500;
  font-size: 0.95rem;
  transition: all 0.2s ease;
}

.nav-item:hover {
  background-color: var(--bg-color);
  color: var(--text-main);
}

.nav-item.active {
  background-color: var(--primary-light);
  color: var(--primary);
  font-weight: 600;
}

.nav-icon {
  flex-shrink: 0;
}

.sidebar-footer {
  padding: 16px;
  border-top: 1px solid var(--border);
}

.logout-btn {
  width: 100%;
  background: transparent;
  border: none;
  cursor: pointer;
  color: var(--danger);
}

.logout-btn:hover {
  background-color: var(--danger-bg);
  color: var(--danger);
}
</style>
