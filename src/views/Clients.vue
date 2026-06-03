<script setup>
import { ref, inject, computed } from 'vue';
import Modal from '../components/Modal.vue';
import Dropdown from '../components/Dropdown.vue';
import { Search, ChevronDown, Plus, Eye, ChevronLeft, ChevronRight } from '@lucide/vue';

const clientsData = [
  { id: 1, initials: 'JS', name: 'João Silva', phone: '(11) 99999-1111', email: 'joao.silva@email.com', city: 'São Paulo - SP', proposals: 3, lastActivity: 'Hoje, 10:30', color: 'purple' },
  { id: 2, initials: 'MO', name: 'Maria Oliveira', phone: '(11) 98888-2222', email: 'maria.oliveira@email.com', city: 'Campinas - SP', proposals: 1, lastActivity: 'Hoje, 09:15', color: 'blue' },
  { id: 3, initials: 'CS', name: 'Carlos Santos', phone: '(11) 97777-3333', email: 'carlos.santos@email.com', city: 'Guarulhos - SP', proposals: 0, lastActivity: 'Ontem, 16:45', color: 'green' },
  { id: 4, initials: 'AP', name: 'Ana Paula', phone: '(11) 96666-4444', email: 'ana.paula@email.com', city: 'São Paulo - SP', proposals: 1, lastActivity: 'Ontem, 14:20', color: 'purple' },
  { id: 5, initials: 'RF', name: 'Rafael Ferreira', phone: '(11) 95555-5555', email: 'rafael.ferreira@email.com', city: 'Osasco - SP', proposals: 2, lastActivity: '12/05/2024', color: 'yellow' },
  { id: 6, initials: 'JC', name: 'Juliana Costa', phone: '(11) 94444-6666', email: 'juliana.costa@email.com', city: 'São Paulo - SP', proposals: 1, lastActivity: '10/05/2024', color: 'purple' },
];

const clients = ref([...clientsData]);

const showClientModal = ref(false);
const showViewModal = ref(false);
const selectedClient = ref(null);
const showToast = inject('showToast');

const submitClient = () => {
  showClientModal.value = false;
  if (showToast) {
    showToast('Cliente adicionado com sucesso!', 'success');
  }
};

const viewClient = (client) => {
  selectedClient.value = client;
  showViewModal.value = true;
};

const handleEdit = (item) => {
  if (showToast) showToast(`Editar cliente ${item.name}`, 'info');
};

const handleDelete = (item) => {
  if (showToast) showToast(`Cliente ${item.name} excluído!`, 'error');
  clients.value = clients.value.filter(c => c.id !== item.id);
};

// Search and Pagination
const searchQuery = ref('');
const currentPage = ref(1);
const itemsPerPage = 4;

const filteredItems = computed(() => {
  if (!searchQuery.value) return clients.value;
  const q = searchQuery.value.toLowerCase();
  return clients.value.filter(item => 
    item.name.toLowerCase().includes(q) || 
    item.email.toLowerCase().includes(q) ||
    item.phone.includes(q)
  );
});

const totalPages = computed(() => Math.ceil(filteredItems.value.length / itemsPerPage) || 1);

const paginatedItems = computed(() => {
  const start = (currentPage.value - 1) * itemsPerPage;
  const end = start + itemsPerPage;
  return filteredItems.value.slice(start, end);
});

const prevPage = () => { if (currentPage.value > 1) currentPage.value--; };
const nextPage = () => { if (currentPage.value < totalPages.value) currentPage.value++; };
const setPage = (page) => { currentPage.value = page; };
</script>

<template>
  <div class="clients">
    <header class="page-header">
      <div>
        <h1 class="page-title">Clientes</h1>
        <p class="page-subtitle">Gerencie seus clientes e acompanhe o histórico.</p>
      </div>
      <button class="btn-primary" @click="showClientModal = true">
        <Plus size="18" />
        Novo cliente
      </button>
    </header>

    <div class="card data-card">
      <div class="data-toolbar">
        <div class="search-box">
          <Search size="18" class="search-icon" />
          <input type="text" v-model="searchQuery" placeholder="Buscar por nome, e-mail ou telefone..." @input="currentPage = 1" />
        </div>
        <div class="filter-actions">
          <div class="dropdown">
            <span>Todos os clientes</span>
            <ChevronDown size="16" />
          </div>
          <div class="dropdown">
            <span>Mais recentes</span>
            <ChevronDown size="16" />
          </div>
        </div>
      </div>

      <table class="data-table">
        <thead>
          <tr>
            <th>Cliente</th>
            <th>Contato</th>
            <th>Localização</th>
            <th class="text-center">Propostas</th>
            <th>Última Atividade</th>
            <th></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in paginatedItems" :key="item.id">
            <td>
              <div class="client-info-cell">
                <div :class="`client-avatar ${item.color}`">
                  {{ item.initials }}
                </div>
                <span class="font-medium">{{ item.name }}</span>
              </div>
            </td>
            <td>
              <div class="contact-info">
                <span>{{ item.phone }}</span>
                <span class="text-xs text-muted">{{ item.email }}</span>
              </div>
            </td>
            <td>{{ item.city }}</td>
            <td class="text-center font-medium">{{ item.proposals }}</td>
            <td class="text-muted">{{ item.lastActivity }}</td>
            <td class="actions-col">
              <div class="actions-cell">
                <button class="icon-btn" @click="viewClient(item)"><Eye size="18" /></button>
                <Dropdown :item="item" @edit="handleEdit" @delete="handleDelete" />
              </div>
            </td>
          </tr>
          <tr v-if="paginatedItems.length === 0">
            <td colspan="6" class="text-center" style="padding: 32px; color: var(--text-muted);">
              Nenhum cliente encontrado para a busca "{{ searchQuery }}".
            </td>
          </tr>
        </tbody>
      </table>

      <div class="pagination">
        <span class="pagination-info">Mostrando {{ (currentPage - 1) * itemsPerPage + 1 }} até {{ Math.min(currentPage * itemsPerPage, filteredItems.length) }} de {{ filteredItems.length }} resultados</span>
        <div class="pagination-controls">
          <button class="page-btn" @click="prevPage" :disabled="currentPage === 1"><ChevronLeft size="16" /></button>
          <button 
            v-for="p in totalPages" 
            :key="p" 
            class="page-btn" 
            :class="{ active: currentPage === p }"
            @click="setPage(p)"
          >
            {{ p }}
          </button>
          <button class="page-btn" @click="nextPage" :disabled="currentPage === totalPages"><ChevronRight size="16" /></button>
        </div>
      </div>
    </div>

    <!-- Modals (omitted from styling below for brevity) -->
    <Modal :show="showClientModal" title="Novo Cliente" @close="showClientModal = false">
      <form @submit.prevent="submitClient" style="display: flex; flex-direction: column; gap: 16px;">
        <div style="display: flex; flex-direction: column; gap: 8px;">
          <label style="font-size: 0.85rem; font-weight: 500;">Nome Completo</label>
          <input type="text" placeholder="Ex: João da Silva" style="width: 100%; padding: 10px; border: 1px solid var(--border); border-radius: var(--radius-md); font-family: var(--font-family);" required />
        </div>
        <div style="display: flex; flex-direction: column; gap: 8px;">
          <label style="font-size: 0.85rem; font-weight: 500;">E-mail</label>
          <input type="email" placeholder="Ex: joao@email.com" style="width: 100%; padding: 10px; border: 1px solid var(--border); border-radius: var(--radius-md); font-family: var(--font-family);" required />
        </div>
        <div style="display: flex; gap: 16px;">
          <div style="display: flex; flex-direction: column; gap: 8px; flex: 1;">
            <label style="font-size: 0.85rem; font-weight: 500;">Telefone / Celular</label>
            <input type="text" placeholder="Ex: (11) 99999-9999" style="width: 100%; padding: 10px; border: 1px solid var(--border); border-radius: var(--radius-md); font-family: var(--font-family);" required />
          </div>
          <div style="display: flex; flex-direction: column; gap: 8px; flex: 1;">
            <label style="font-size: 0.85rem; font-weight: 500;">Cidade</label>
            <input type="text" placeholder="Ex: São Paulo - SP" style="width: 100%; padding: 10px; border: 1px solid var(--border); border-radius: var(--radius-md); font-family: var(--font-family);" required />
          </div>
        </div>
        <div style="display: flex; justify-content: flex-end; gap: 12px; margin-top: 16px;">
          <button type="button" @click="showClientModal = false" style="padding: 10px 16px; border: 1px solid var(--border); border-radius: var(--radius-md); background: white; cursor: pointer;">Cancelar</button>
          <button type="submit" class="btn-primary">Salvar Cliente</button>
        </div>
      </form>
    </Modal>

    <!-- Modal Visualizar Cliente -->
    <Modal :show="showViewModal" title="Detalhes do Cliente" @close="showViewModal = false">
      <div v-if="selectedClient" style="display: flex; flex-direction: column; gap: 24px;">
        <div style="display: flex; align-items: center; gap: 16px;">
          <div :class="`client-avatar ${selectedClient.color}`" style="width: 56px; height: 56px; font-size: 1.2rem;">
            {{ selectedClient.initials }}
          </div>
          <div>
            <h3 style="font-size: 1.2rem; font-weight: 600; margin-bottom: 4px;">{{ selectedClient.name }}</h3>
            <span style="color: var(--text-muted); font-size: 0.9rem;">Cliente ativo (Última ativ: {{ selectedClient.lastActivity }})</span>
          </div>
        </div>
        
        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 16px; background-color: var(--bg-color); padding: 16px; border-radius: var(--radius-md);">
          <div>
            <span style="font-size: 0.8rem; color: var(--text-muted); display: block;">E-mail</span>
            <span style="font-weight: 500;">{{ selectedClient.email }}</span>
          </div>
          <div>
            <span style="font-size: 0.8rem; color: var(--text-muted); display: block;">Telefone</span>
            <span style="font-weight: 500;">{{ selectedClient.phone }}</span>
          </div>
          <div>
            <span style="font-size: 0.8rem; color: var(--text-muted); display: block;">Cidade</span>
            <span style="font-weight: 500;">{{ selectedClient.city }}</span>
          </div>
          <div>
            <span style="font-size: 0.8rem; color: var(--text-muted); display: block;">Propostas em andamento</span>
            <span style="font-weight: 600;">{{ selectedClient.proposals }} propostas</span>
          </div>
        </div>
        
        <div style="display: flex; justify-content: flex-end; gap: 12px; margin-top: 8px; border-top: 1px solid var(--border); padding-top: 16px;">
          <button class="btn-primary" @click="showViewModal = false">Fechar</button>
        </div>
      </div>
    </Modal>
  </div>
</template>

<style scoped>
.clients {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.page-header {
  display: flex;
  justify-content: space-between;
  align-items: flex-end;
}

.page-title {
  font-size: 1.75rem;
  font-weight: 700;
  margin-bottom: 4px;
}

.page-subtitle {
  color: var(--text-muted);
  font-size: 0.95rem;
}

.data-card {
  padding: 0;
  overflow: visible;
}

.data-toolbar {
  padding: 20px 24px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid var(--border);
}

.search-box {
  position: relative;
  width: 320px;
}

.search-icon {
  position: absolute;
  left: 12px;
  top: 50%;
  transform: translateY(-50%);
  color: var(--text-muted);
}

.search-box input {
  width: 100%;
  padding: 10px 12px 10px 40px;
  border: 1px solid var(--border);
  border-radius: var(--radius-md);
  font-family: var(--font-family);
  font-size: 0.9rem;
  outline: none;
  transition: all 0.2s;
}

.search-box input:focus {
  border-color: var(--primary);
  box-shadow: 0 0 0 3px var(--primary-light);
}

.filter-actions {
  display: flex;
  gap: 12px;
}

.dropdown {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  border: 1px solid var(--border);
  border-radius: var(--radius-md);
  font-size: 0.9rem;
  color: var(--text-main);
  font-weight: 500;
  cursor: pointer;
  background-color: white;
}

.client-info-cell {
  display: flex;
  align-items: center;
  gap: 12px;
}

.client-avatar {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 600;
  font-size: 0.85rem;
  flex-shrink: 0;
}

.client-avatar.purple { background-color: var(--primary-light); color: var(--primary); }
.client-avatar.blue { background-color: #DBEAFE; color: #2563EB; }
.client-avatar.green { background-color: #DCFCE7; color: #16A34A; }
.client-avatar.yellow { background-color: #FEF9C3; color: #CA8A04; }

.contact-info {
  display: flex;
  flex-direction: column;
}

.text-xs {
  font-size: 0.8rem;
}

.actions-cell {
  display: flex;
  gap: 4px;
  justify-content: flex-end;
}

.pagination {
  padding: 16px 24px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-top: 1px solid var(--border);
}

.pagination-info {
  font-size: 0.85rem;
  color: var(--text-muted);
}

.pagination-controls {
  display: flex;
  gap: 4px;
}

.page-btn {
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid var(--border);
  background-color: white;
  border-radius: var(--radius-md);
  font-size: 0.9rem;
  font-weight: 500;
  color: var(--text-main);
  cursor: pointer;
  transition: all 0.2s;
}

.page-btn:hover:not(:disabled) {
  background-color: var(--bg-color);
}

.page-btn.active {
  background-color: var(--primary);
  color: white;
  border-color: var(--primary);
}

.page-btn:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
</style>
