<script setup>
import { ref, inject } from 'vue';
import Modal from '../components/Modal.vue';
import { 
  ShoppingCart, 

  Users, 
  FileText, 
  DollarSign, 
  Plus, 
  MoreVertical, 
  TrendingUp, 
  Car,
  ChevronDown
} from '@lucide/vue';

// Data placeholders for mockup
const summaryCards = [
  { title: 'Vendas', value: '18', change: '+ 28% vs mês anterior', changeType: 'positive', icon: ShoppingCart, color: 'purple' },
  { title: 'Clientes', value: '127', change: '+ 16% vs mês anterior', changeType: 'positive', icon: Users, color: 'green' },
  { title: 'Propostas enviadas', value: '43', change: '+ 12% vs mês anterior', changeType: 'positive', icon: FileText, color: 'blue' },
  { title: 'Comissões', value: 'R$ 18.450,00', change: '+ 32% vs mês anterior', changeType: 'positive', icon: DollarSign, color: 'yellow' },
];

const latestProposals = [
  { initials: 'JS', client: 'João Silva', vehicle: 'Toyota Corolla XEi 2.0', value: 'R$ 110.000,00', status: 'Em análise', updated: 'Hoje, 10:30', color: 'purple', statusColor: 'warning' },
  { initials: 'MO', client: 'Maria Oliveira', vehicle: 'Hyundai HB20 Comfort', value: 'R$ 72.000,00', status: 'Aprovada', updated: 'Hoje, 09:15', color: 'green', statusColor: 'success' },
  { initials: 'CS', client: 'Carlos Santos', vehicle: 'Jeep Compass Limited', value: 'R$ 135.000,00', status: 'Aguardando docs', updated: 'Ontem, 16:45', color: 'blue', statusColor: 'info' },
  { initials: 'AP', client: 'Ana Paula', vehicle: 'Fiat Toro Volcano', value: 'R$ 115.000,00', status: 'Enviada', updated: 'Ontem, 14:20', color: 'purple', statusColor: 'primary' },
  { initials: 'RF', client: 'Rafael Ferreira', vehicle: 'VW Nivus Highline', value: 'R$ 98.000,00', status: 'Reprovada', updated: '12/05/2024', color: 'yellow', statusColor: 'danger' },
];

const showProposalModal = ref(false);
const showToast = inject('showToast');

const submitProposal = () => {
  showProposalModal.value = false;
  if (showToast) {
    showToast('Proposta gerada com sucesso!', 'success');
  }
};
</script>

<template>
  <div class="overview">
    <header class="page-header">
      <div>
        <h1 class="page-title">Olá, Auto Motors! <span class="wave">👋</span></h1>
        <p class="page-subtitle">Acompanhe o desempenho das suas vendas e propostas.</p>
      </div>
      <div class="header-actions">
        <div class="date-picker">
          <Calendar size="16" />
          <span>12 a 18 de Mai de 2024</span>
          <ChevronDown size="16" />
        </div>
        <button class="btn-primary" @click="showProposalModal = true">
          <Plus size="18" />
          Nova Proposta
        </button>
      </div>
    </header>

    <div class="summary-grid">
      <div class="summary-card card" v-for="card in summaryCards" :key="card.title">
        <div :class="['card-icon', card.color]">
          <component :is="card.icon" size="24" />
        </div>
        <div class="card-content">
          <h3 class="card-title">{{ card.title }}</h3>
          <p class="card-value">{{ card.value }}</p>
          <div class="card-change" :class="card.changeType === 'positive' ? 'text-success' : 'text-danger'">
            <TrendingUp v-if="card.changeType === 'positive'" size="14" />
            <TrendingDown v-else size="14" />
            <span>{{ card.change }}</span>
          </div>
        </div>
      </div>
    </div>

    <div class="dashboard-grid">
      <!-- Latest Proposals -->
      <div class="card latest-proposals">
        <div class="card-header">
          <h2 class="section-title">Últimas propostas</h2>
          <router-link to="/proposals" class="view-all">Ver todas</router-link>
        </div>
        
        <div class="table-responsive">
          <table class="data-table">
            <thead>
              <tr>
                <th>Cliente</th>
                <th>Veículo</th>
                <th>Valor do bem</th>
                <th>Status</th>
                <th>Atualizado</th>
                <th></th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(proposal, index) in latestProposals" :key="index">
                <td>
                  <div class="client-cell">
                    <div class="avatar" :class="'avatar-' + proposal.color">{{ proposal.initials }}</div>
                    <span class="client-name">{{ proposal.client }}</span>
                  </div>
                </td>
                <td class="vehicle-name">{{ proposal.vehicle }}</td>
                <td class="font-medium">{{ proposal.value }}</td>
                <td>
                  <span class="badge" :class="proposal.statusColor">{{ proposal.status }}</span>
                </td>
                <td class="text-muted">{{ proposal.updated }}</td>
                <td>
                  <button class="icon-btn-small"><MoreVertical size="16" /></button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>

      <!-- Quick Actions and Inventory side -->
      <div class="side-column">
        <div class="card quick-actions">
          <h2 class="section-title">Ações rápidas</h2>
          <div class="action-list">
            <button class="action-item" @click="$router.push('/proposals')">
              <div class="action-icon icon-purple"><Plus size="20" /></div>
              <div class="action-text">
                <span class="action-title">Nova Proposta</span>
                <span class="action-desc">Gerar proposta para um cliente</span>
              </div>
              <ChevronRight size="16" class="action-arrow" />
            </button>
            <button class="action-item" @click="$router.push('/inventory')">
              <div class="action-icon icon-green"><Car size="20" /></div>
              <div class="action-text">
                <span class="action-title">Adicionar Veículo</span>
                <span class="action-desc">Cadastrar veículo no estoque</span>
              </div>
              <ChevronRight size="16" class="action-arrow" />
            </button>
            <button class="action-item" @click="$router.push('/clients')">
              <div class="action-icon icon-blue"><Users size="20" /></div>
              <div class="action-text">
                <span class="action-title">Novo Cliente</span>
                <span class="action-desc">Cadastrar novo cliente</span>
              </div>
              <ChevronRight size="16" class="action-arrow" />
            </button>
            <button class="action-item">
              <div class="action-icon icon-yellow"><MessageSquare size="20" /></div>
              <div class="action-text">
                <span class="action-title">Falar com Especialista</span>
                <span class="action-desc">Tire dúvidas com nosso time</span>
              </div>
              <ChevronRight size="16" class="action-arrow" />
            </button>
          </div>
        </div>

        <div class="card inventory-summary">
          <div class="card-header">
            <h2 class="section-title">Estoque de veículos</h2>
            <router-link to="/inventory" class="view-all">Ver todos</router-link>
          </div>
          <div class="inventory-total">
            <div class="total-icon"><Car size="24" /></div>
            <div class="total-text">
              <span class="total-value">27</span>
              <span class="total-label">Veículos cadastrados</span>
            </div>
          </div>
          <div class="inventory-list">
            <div class="inv-item">
              <span>Carros</span>
              <strong>19</strong>
            </div>
            <div class="inv-item">
              <span>Motos</span>
              <strong>5</strong>
            </div>
            <div class="inv-item">
              <span>Caminhões</span>
              <strong>3</strong>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal Nova Proposta -->
    <Modal :show="showProposalModal" title="Nova Proposta" @close="showProposalModal = false">
      <form @submit.prevent="submitProposal" style="display: flex; flex-direction: column; gap: 16px;">
        <div style="display: flex; flex-direction: column; gap: 8px;">
          <label style="font-size: 0.85rem; font-weight: 500;">Selecione o Cliente</label>
          <select style="width: 100%; padding: 10px; border: 1px solid var(--border); border-radius: var(--radius-md); font-family: var(--font-family);">
            <option>João Silva</option>
            <option>Maria Oliveira</option>
          </select>
        </div>
        <div style="display: flex; flex-direction: column; gap: 8px;">
          <label style="font-size: 0.85rem; font-weight: 500;">Veículo de Interesse</label>
          <input type="text" placeholder="Ex: Toyota Corolla XEi 2.0" style="width: 100%; padding: 10px; border: 1px solid var(--border); border-radius: var(--radius-md); font-family: var(--font-family);" />
        </div>
        <div style="display: flex; flex-direction: column; gap: 8px;">
          <label style="font-size: 0.85rem; font-weight: 500;">Valor da Proposta (R$)</label>
          <input type="text" placeholder="Ex: 110.000,00" style="width: 100%; padding: 10px; border: 1px solid var(--border); border-radius: var(--radius-md); font-family: var(--font-family);" />
        </div>
        <div style="display: flex; justify-content: flex-end; gap: 12px; margin-top: 16px;">
          <button type="button" @click="showProposalModal = false" style="padding: 10px 16px; border: 1px solid var(--border); border-radius: var(--radius-md); background: white; cursor: pointer;">Cancelar</button>
          <button type="submit" class="btn-primary">Gerar Proposta</button>
        </div>
      </form>
    </Modal>
  </div>
</template>

<script>
// Just importing the rest of the icons for the template above that we forgot in setup
import { Calendar, ChevronRight, MessageSquare } from '@lucide/vue';
export default {
  components: { Calendar, ChevronRight, MessageSquare }
}
</script>

<style scoped>
.overview {
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
  font-size: 2rem;
  font-weight: 700;
  margin-bottom: 4px;
  color: var(--text-main);
}

.wave {
  display: inline-block;
  animation: wave 2.5s infinite;
  transform-origin: 70% 70%;
}

@keyframes wave {
  0% { transform: rotate(0deg); }
  10% { transform: rotate(14deg); }
  20% { transform: rotate(-8deg); }
  30% { transform: rotate(14deg); }
  40% { transform: rotate(-4deg); }
  50% { transform: rotate(10deg); }
  60% { transform: rotate(0deg); }
  100% { transform: rotate(0deg); }
}

.page-subtitle {
  color: var(--text-muted);
  font-size: 0.95rem;
}

.header-actions {
  display: flex;
  gap: 16px;
  align-items: center;
}

.date-picker {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 16px;
  background-color: var(--surface);
  border: 1px solid var(--border);
  border-radius: var(--radius-md);
  font-size: 0.85rem;
  color: var(--text-main);
  font-weight: 500;
  cursor: pointer;
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
  width: 56px;
  height: 56px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
}

.card-icon.purple { background-color: var(--primary-light); color: var(--primary); }
.card-icon.blue { background-color: #DBEAFE; color: #2563EB; }
.card-icon.green { background-color: #DCFCE7; color: #16A34A; }
.card-icon.yellow { background-color: #FEF9C3; color: #CA8A04; }

.card-content {
  display: flex;
  flex-direction: column;
  flex: 1;
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
  color: var(--text-main);
}

.card-change {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  font-size: 0.75rem;
  font-weight: 600;
}

.card-change.positive { color: var(--success); }

.dashboard-grid {
  display: grid;
  grid-template-columns: 2fr 1fr;
  gap: 24px;
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

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 20px;
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

.client-cell {
  display: flex;
  align-items: center;
  gap: 12px;
}

.avatar {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.75rem;
  font-weight: 600;
}

.avatar-purple { background-color: var(--primary-light); color: var(--primary); }
.avatar-green { background-color: #DCFCE7; color: #16A34A; }
.avatar-blue { background-color: #DBEAFE; color: #2563EB; }
.client-avatar.yellow { background-color: #FEF9C3; color: #CA8A04; }

.table-responsive {
  width: 100%;
  overflow-x: auto;
  -webkit-overflow-scrolling: touch;
}

@media (max-width: 1024px) {
  .dashboard-grid {
    grid-template-columns: 1fr;
  }
}

@media (max-width: 768px) {
  .page-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 16px;
  }
}

.client-name { font-weight: 500; }
.vehicle-name { color: var(--text-main); }
.font-medium { font-weight: 500; }
.text-muted { color: var(--text-muted); font-size: 0.85rem; }

.icon-btn-small {
  color: var(--text-muted);
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 4px;
}

.icon-btn-small:hover {
  background-color: var(--border);
  color: var(--text-main);
}

.side-column {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

.action-list {
  display: flex;
  flex-direction: column;
  gap: 12px;
}

.action-item {
  display: flex;
  align-items: center;
  padding: 12px;
  border: 1px solid var(--border);
  border-radius: var(--radius-lg);
  background-color: transparent;
  width: 100%;
  text-align: left;
  transition: all 0.2s;
}

.action-item:hover {
  border-color: var(--primary);
  box-shadow: var(--shadow-sm);
  background-color: var(--primary-light);
}

.action-item:hover .action-arrow {
  color: var(--primary);
  transform: translateX(4px);
}

.action-icon {
  width: 40px;
  height: 40px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  justify-content: center;
  margin-right: 16px;
}

.icon-purple { background-color: var(--primary); color: white; }
.icon-green { background-color: #10B981; color: white; }
.icon-blue { background-color: #3B82F6; color: white; }
.icon-yellow { background-color: #F59E0B; color: white; }

.action-text {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.action-title {
  font-weight: 600;
  font-size: 0.95rem;
  color: var(--text-main);
}

.action-desc {
  font-size: 0.75rem;
  color: var(--text-muted);
}

.action-arrow {
  color: var(--text-muted);
  transition: all 0.2s;
}

.inventory-total {
  display: flex;
  align-items: center;
  gap: 16px;
  padding: 16px;
  background-color: var(--primary-light);
  border-radius: var(--radius-lg);
  margin-bottom: 20px;
}

.total-icon {
  width: 48px;
  height: 48px;
  background-color: var(--primary);
  color: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.total-text {
  display: flex;
  flex-direction: column;
}

.total-value {
  font-size: 1.5rem;
  font-weight: 700;
  line-height: 1.2;
}

.total-label {
  font-size: 0.8rem;
  color: var(--text-muted);
}

.inventory-list {
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.inv-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  font-size: 0.9rem;
  padding-bottom: 12px;
  border-bottom: 1px solid var(--border);
}

.inv-item:last-child {
  border-bottom: none;
  padding-bottom: 0;
}
</style>
