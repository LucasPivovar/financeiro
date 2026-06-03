<script setup>
import Sidebar from './components/Sidebar.vue'
import Topbar from './components/Topbar.vue'
import Toast from './components/Toast.vue'
import { provide, computed } from 'vue'
import { useToast } from './composables/useToast'
import { useRoute } from 'vue-router'

const { addToast } = useToast()
provide('showToast', addToast)

const route = useRoute()

// Check if route is an auth route to hide sidebar/topbar
const isAuthRoute = computed(() => {
  return ['Login', 'Register', 'TwoFactor'].includes(route.name)
})
</script>

<template>
  <div class="app-wrapper">
    <!-- Auth Layout -->
    <div v-if="isAuthRoute" class="auth-layout">
      <router-view />
    </div>

    <!-- Dashboard Layout -->
    <div v-else class="dashboard-layout">
      <Sidebar />
      <div class="dashboard-main">
        <Topbar />
        <main class="page-content">
          <router-view />
        </main>
      </div>
    </div>
    
    <Toast />
  </div>
</template>

<style scoped>
.app-wrapper {
  min-height: 100vh;
  background-color: var(--bg-color);
}

.auth-layout {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}

.dashboard-layout {
  display: flex;
  min-height: 100vh;
}

.dashboard-main {
  flex: 1;
  display: flex;
  flex-direction: column;
  min-width: 0; /* Important for preventing overflow */
}

.page-content {
  flex: 1;
  padding: 32px;
  width: 100%;
}
</style>
