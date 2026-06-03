<script setup>
import { ref, computed } from 'vue';
import { DollarSign, Clock, CheckCircle, TrendingUp, ChevronDown, Search, ChevronLeft, ChevronRight } from '@lucide/vue';

const summaryCards = [
  { title: 'Comissões do mês', value: 'R$ 18.450,00', change: '+ 32% vs mês anterior', changeType: 'positive', icon: DollarSign, color: 'green' },
  { title: 'Comissões pendentes', value: 'R$ 6.250,00', subtitle: 'Em aguardo de pagamento', icon: Clock, color: 'blue' },
  { title: 'Comissões pagas', value: 'R$ 12.200,00', change: '+ Recebidas este mês', changeType: 'positive', icon: CheckCircle, color: 'yellow' },
  { title: 'Ticket médio', value: 'R$ 3.690,00', subtitle: 'Por venda realizada', icon: TrendingUp, color: 'purple' },
];

const historyData = [
  { date: '18/05/2024', proposal: '#1247', client: 'João Silva', value: 'R$ 110.000,00', commission: 'R$ 2.750,00', status: 'Pendente', statusColor: 'warning' },
  { date: '18/05/2024', proposal: '#1246', client: 'Maria Oliveira', value: 'R$ 72.000,00', commission: 'R$ 1.800,00', status: 'Pendente', statusColor: 'warning' },
  { date: '17/05/2024', proposal: '#1245', client: 'Carlos Santos', value: 'R$ 135.000,00', commission: 'R$ 3.375,00', status: 'Pendente', statusColor: 'warning' },
  { date: '16/05/2024', proposal: '#1244', client: 'Ana Paula', value: 'R$ 115.000,00', commission: 'R$ 2.875,00', status: 'Paga', statusColor: 'success' },
  { date: '15/05/2024', proposal: '#1243', client: 'Rafael Ferreira', value: 'R$ 98.000,00', commission: 'R$ 2.450,00', status: 'Paga', statusColor: 'success' },
  { date: '14/05/2024', proposal: '#1242', client: 'Juliana Costa', value: 'R$ 45.000,00', commission: 'R$ 1.125,00', status: 'Paga', statusColor: 'success' },
  { date: '12/05/2024', proposal: '#1241', client: 'Felipe Rocha', value: 'R$ 115.000,00', commission: 'R$ 2.875,00', status: 'Paga', statusColor: 'success' },
  { date: '10/05/2024', proposal: '#1240', client: 'Camila Lima', value: 'R$ 155.000,00', commission: 'R$ 3.875,00', status: 'Paga', statusColor: 'success' },
];

const history = ref([...historyData]);
const filterStatus = ref('');
const searchQuery = ref('');
const currentPage = ref(1);
const itemsPerPage = 4;

const filteredItems = computed(() => {
  let result = history.value;
  if (searchQuery.value) {
    const q = searchQuery.value.toLowerCase();
    result = result.filter(item => 
      item.client.toLowerCase().includes(q) || 
      item.proposal.toLowerCase().includes(q)
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
  <div class="commissions">
    <header class="page-header">
      <div>
        <h1 class="page-title">Comissões</h1>
        <p class="page-subtitle">Acompanhe suas comissões e pagamentos.</p>
      </div>
      <div class="header-actions">
        <div class="dropdown">
          <span>Este mês</span>
          <ChevronDown size="16" />
        </div>
      </div>
    </header>

    <div class="summary-grid">
      <div v-for="(card, index) in summaryCards" :key="index" class="card summary-card">
        <div class="card-icon" :class="card.color">
          <component :is="card.icon" size="24" />
        </div>
        <div class="card-content">
          <h3 class="card-title">{{ card.title }}</h3>
          <div class="card-value" :class="{ 'text-green': index === 0 }">{{ card.value }}</div>
          <div v-if="card.change" class="card-change" :class="card.changeType">
            <TrendingUp size="14" v-if="card.changeType === 'positive' && index === 0" />
            {{ card.change }}
          </div>
          <div v-if="card.subtitle" class="card-subtitle">
            {{ card.subtitle }}
          </div>
        </div>
      </div>
    </div>

    <div class="card table-card">
      <div class="card-header">
        <h2 class="section-title">Histórico de comissões</h2>
        <router-link to="/reports" class="view-all">Ver todas</router-link>
      </div>

      <div class="data-toolbar">
        <div class="search-box">
          <Search size="18" class="search-icon" />
          <input type="text" v-model="searchQuery" placeholder="Buscar por cliente ou proposta..." @input="currentPage = 1" />
        </div>
        <div class="filter-actions">
          <select v-model="filterStatus" class="filter-select" @change="currentPage = 1">
            <option value="">Todos os status</option>
            <option value="Pendente">Pendente</option>
            <option value="Paga">Paga</option>
          </select>
        </div>
      </div>

      <div class="table-responsive">
        <table class="data-table">
          <thead>
            <tr>
              <th>Data</th>
              <th>Proposta</th>
              <th>Cliente</th>
              <th>Valor do bem</th>
              <th>Comissão</th>
              <th>Status</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(item, index) in paginatedItems" :key="index">
              <td class="text-muted">{{ item.date }}</td>
              <td>
                <span class="proposal-id">{{ item.proposal }}</span>
              </td>
              <td class="font-medium">{{ item.client }}</td>
              <td>{{ item.value }}</td>
              <td class="font-medium">{{ item.commission }}</td>
              <td>
                <span class="badge" :class="`badge-${item.statusColor}`">{{ item.status }}</span>
              </td>
            </tr>
            <tr v-if="paginatedItems.length === 0">
              <td colspan="6" class="text-center" style="padding: 32px; color: var(--text-muted);">
                Nenhuma comissão encontrada.
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
  </div>
</template>

<style scoped>
.commissions {
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

.summary-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 24px;
}

.summary-card {
  display: flex;
  flex-direction: row;
  align-items: center;
  gap: 16px;
  padding: 24px;
}

.card-icon {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.card-icon.green { background-color: #DCFCE7; color: #16A34A; }
.card-icon.blue { background-color: #DBEAFE; color: #2563EB; }
.card-icon.yellow { background-color: #FEF9C3; color: #CA8A04; }
.card-icon.purple { background-color: var(--primary-light); color: var(--primary); }

.card-content {
  display: flex;
  flex-direction: column;
}

.card-title {
  font-size: 0.85rem;
  font-weight: 500;
  color: var(--text-muted);
  margin-bottom: 4px;
}

.card-value {
  font-size: 1.5rem;
  font-weight: 700;
  margin-bottom: 4px;
}

.text-green {
  color: #16A34A;
}

.card-change {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  font-size: 0.75rem;
  font-weight: 600;
}

.card-change.positive { color: var(--success); }

.card-subtitle {
  font-size: 0.75rem;
  color: var(--text-muted);
}

.table-card {
  padding: 0;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 20px 24px;
  border-bottom: 1px solid var(--border);
}

.section-title {
  font-size: 1.1rem;
  font-weight: 600;
}

.view-all {
  color: var(--primary);
  font-size: 0.85rem;
  font-weight: 600;
  text-decoration: none;
}

.view-all:hover {
  text-decoration: underline;
}

.proposal-id {
  color: var(--primary);
  font-weight: 600;
}

.text-muted {
  color: var(--text-muted);
}

.font-medium {
  font-weight: 500;
}

.table-responsive {
  width: 100%;
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
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
