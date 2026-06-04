<script setup>
import { ref, inject, computed } from 'vue';
import Modal from '../components/Modal.vue';
import NewProposalModal from '../components/NewProposalModal.vue';
import Dropdown from '../components/Dropdown.vue';
import { Search, ChevronDown, Plus, Eye, ChevronLeft, ChevronRight } from '@lucide/vue';

const proposalsData = [
  { id: '#1247', client: 'João Silva', phone: '(11) 90000-1111', vehicle: 'Toyota Corolla XEi 2.0', year: 'Ano 2023', value: 'R$ 110.000,00', status: 'Em análise', statusColor: 'warning', created: 'Hoje, 10:30' },
  { id: '#1246', client: 'Maria Oliveira', phone: '(11) 91111-2222', vehicle: 'Honda Civic Touring', year: 'Ano 2022', value: 'R$ 145.000,00', status: 'Aprovada', statusColor: 'success', created: 'Hoje, 09:15' },
  { id: '#1245', client: 'Carlos Santos', phone: '(11) 92222-3333', vehicle: 'Honda CB 500F', year: 'Ano 2023', value: 'R$ 35.900,00', status: 'Em análise', statusColor: 'warning', created: 'Ontem, 16:45' },
  { id: '#1244', client: 'Ana Paula', phone: '(11) 96666-4444', vehicle: 'Fiat Toro Volcano', year: 'Ano 2022', value: 'R$ 115.000,00', status: 'Enviada', statusColor: 'primary', created: 'Ontem, 14:20' },
  { id: '#1243', client: 'Rafael Ferreira', phone: '(11) 95555-5555', vehicle: 'VW Nivus Highline', year: 'Ano 2023', value: 'R$ 98.000,00', status: 'Reprovada', statusColor: 'danger', created: '12/05/2024' },
  { id: '#1242', client: 'Juliana Costa', phone: '(11) 97777-8888', vehicle: 'Yamaha MT-07', year: 'Ano 2024', value: 'R$ 45.000,00', status: 'Aprovada', statusColor: 'success', created: '10/05/2024' },
  { id: '#1241', client: 'Felipe Rocha', phone: '(11) 99888-7777', vehicle: 'Chevrolet Tracker LTZ', year: 'Ano 2022', value: 'R$ 115.000,00', status: 'Em análise', statusColor: 'warning', created: '09/05/2024' },
  { id: '#1240', client: 'Camila Lima', phone: '(11) 93333-2222', vehicle: 'Jeep Compass Limited', year: 'Ano 2023', value: 'R$ 155.000,00', status: 'Enviada', statusColor: 'primary', created: '08/05/2024' },
  { id: '#1239', client: 'Roberto Alves', phone: '(11) 94444-1111', vehicle: 'Scania R450', year: 'Ano 2022', value: 'R$ 450.000,00', status: 'Aprovada', statusColor: 'success', created: '08/05/2024' },
];

const proposals = ref([...proposalsData]);
const filterStatus = ref('');

const showProposalModal = ref(false);
const showViewModal = ref(false);
const selectedProposal = ref(null);
const showToast = inject('showToast');

const submitProposal = () => {
  showProposalModal.value = false;
  if (showToast) {
    showToast('Proposta gerada com sucesso!', 'success');
  }
};

const viewProposal = (proposal) => {
  selectedProposal.value = proposal;
  showViewModal.value = true;
};

const handleEdit = (item) => {
  if (showToast) showToast(`Editar proposta ${item.id}`, 'info');
};

const handleDelete = (item) => {
  if (showToast) showToast(`Proposta ${item.id} excluída!`, 'error');
  proposals.value = proposals.value.filter(p => p.id !== item.id);
};

// Search and Pagination
const searchQuery = ref('');
const currentPage = ref(1);
const itemsPerPage = 4;

const filteredItems = computed(() => {
  let result = proposals.value;
  if (searchQuery.value) {
    const q = searchQuery.value.toLowerCase();
    result = result.filter(item => 
      item.client.toLowerCase().includes(q) || 
      item.vehicle.toLowerCase().includes(q) ||
      item.id.toLowerCase().includes(q)
    );
  }
  if (filterStatus.value) {
    result = result.filter(item => item.status === filterStatus.value);
  }
  return result;
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
  <div class="proposals">
    <header class="page-header">
      <div>
        <h1 class="page-title">Propostas</h1>
        <p class="page-subtitle">Acompanhe todas as propostas enviadas.</p>
      </div>
      <button class="btn-primary" @click="showProposalModal = true">
        <Plus size="18" />
        Nova proposta
      </button>
    </header>

    <div class="card data-card">
      <div class="data-toolbar">
        <div class="search-box">
          <Search size="18" class="search-icon" />
          <input type="text" v-model="searchQuery" placeholder="Buscar por cliente, veículo ou ID..." @input="currentPage = 1" />
        </div>
        <div class="filter-actions">
          <select v-model="filterStatus" class="filter-select" @change="currentPage = 1">
            <option value="">Todos os status</option>
            <option value="Em análise">Em análise</option>
            <option value="Aprovada">Aprovada</option>
            <option value="Enviada">Enviada</option>
            <option value="Reprovada">Reprovada</option>
          </select>
        </div>
      </div>

      <div class="table-responsive">
        <table class="data-table">
          <thead>
            <tr>
              <th>ID</th>
              <th>Cliente</th>
              <th>Veículo de Interesse</th>
              <th>Valor</th>
              <th>Status</th>
              <th>Criado em</th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in paginatedItems" :key="item.id">
              <td class="font-medium text-muted">{{ item.id }}</td>
              <td>
                <div class="client-info">
                  <span class="font-medium">{{ item.client }}</span>
                  <span class="text-xs text-muted">{{ item.phone }}</span>
                </div>
              </td>
              <td>
                <div class="vehicle-info-sm">
                  <span class="font-medium">{{ item.vehicle }}</span>
                  <span class="text-xs text-muted">{{ item.year }}</span>
                </div>
              </td>
              <td class="font-medium">{{ item.value }}</td>
              <td>
                <span :class="`badge badge-${item.statusColor}`">{{ item.status }}</span>
              </td>
              <td class="text-muted">{{ item.created }}</td>
              <td class="actions-col">
                <div class="actions-cell">
                  <button class="icon-btn" @click="viewProposal(item)"><Eye size="18" /></button>
                  <Dropdown :item="item" @edit="handleEdit" @delete="handleDelete" />
                </div>
              </td>
            </tr>
            <tr v-if="paginatedItems.length === 0">
              <td colspan="7" class="text-center" style="padding: 32px; color: var(--text-muted);">
                Nenhuma proposta encontrada para a busca "{{ searchQuery }}".
              </td>
            </tr>
          </tbody>
        </table>
      </div>

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

    <!-- Modal Nova Proposta -->
    <NewProposalModal :show="showProposalModal" @close="showProposalModal = false" @submit="submitProposal" />

    <!-- Modal Visualizar Proposta -->
    <Modal :show="showViewModal" :title="`Detalhes da Proposta ${selectedProposal?.id || ''}`" @close="showViewModal = false">
      <div v-if="selectedProposal" style="display: flex; flex-direction: column; gap: 16px;">
        <div style="display: grid; grid-template-columns: 1fr 1fr; gap: 16px;">
          <div>
            <span style="font-size: 0.8rem; color: var(--text-muted); display: block;">Cliente</span>
            <span style="font-weight: 500;">{{ selectedProposal.client }}</span>
          </div>
          <div>
            <span style="font-size: 0.8rem; color: var(--text-muted); display: block;">Telefone</span>
            <span style="font-weight: 500;">{{ selectedProposal.phone }}</span>
          </div>
          <div>
            <span style="font-size: 0.8rem; color: var(--text-muted); display: block;">Veículo</span>
            <span style="font-weight: 500;">{{ selectedProposal.vehicle }} ({{ selectedProposal.year }})</span>
          </div>
          <div>
            <span style="font-size: 0.8rem; color: var(--text-muted); display: block;">Valor</span>
            <span style="font-weight: 600;">{{ selectedProposal.value }}</span>
          </div>
          <div>
            <span style="font-size: 0.8rem; color: var(--text-muted); display: block;">Data de Criação</span>
            <span style="font-weight: 500;">{{ selectedProposal.created }}</span>
          </div>
          <div>
            <span style="font-size: 0.8rem; color: var(--text-muted); display: block;">Status</span>
            <span :class="`badge badge-${selectedProposal.statusColor}`">{{ selectedProposal.status }}</span>
          </div>
        </div>
        <div style="display: flex; justify-content: flex-end; gap: 12px; margin-top: 16px; border-top: 1px solid var(--border); padding-top: 16px;">
          <button class="btn-primary" @click="showViewModal = false">Fechar</button>
        </div>
      </div>
    </Modal>
  </div>
</template>

<style scoped>
.proposals {
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
  flex-wrap: wrap;
  gap: 16px;
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

.filter-select {
  padding: 10px 32px 10px 16px;
  border: 1px solid var(--border);
  border-radius: var(--radius-md);
  font-family: var(--font-family);
  font-size: 0.9rem;
  color: var(--text-main);
  background-color: white;
  cursor: pointer;
  appearance: none;
  background-image: url("data:image/svg+xml;charset=UTF-8,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='none' stroke='currentColor' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3e%3cpolyline points='6 9 12 15 18 9'%3e%3c/polyline%3e%3c/svg%3e");
  background-repeat: no-repeat;
  background-position: right 10px center;
  background-size: 16px;
  outline: none;
  transition: all 0.2s;
}

.filter-select:focus {
  border-color: var(--primary);
  box-shadow: 0 0 0 3px var(--primary-light);
}

.client-info, .vehicle-info-sm {
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

.table-responsive {
  width: 100%;
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
}

@media (max-width: 768px) {
  .page-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 16px;
  }
  .search-box {
    width: 100%;
  }
  .filter-actions {
    width: 100%;
    flex-wrap: wrap;
  }
  .pagination {
    flex-direction: column;
    gap: 16px;
  }
}
</style>
