<script setup>
import { DollarSign, Clock, CheckCircle, TrendingUp, ChevronDown } from '@lucide/vue';

const summaryCards = [
  { title: 'Comissões do mês', value: 'R$ 18.450,00', change: '+ 32% vs mês anterior', changeType: 'positive', icon: DollarSign, color: 'green' },
  { title: 'Comissões pendentes', value: 'R$ 6.250,00', subtitle: 'Em aguardo de pagamento', icon: Clock, color: 'blue' },
  { title: 'Comissões pagas', value: 'R$ 12.200,00', change: '+ Recebidas este mês', changeType: 'positive', icon: CheckCircle, color: 'yellow' },
  { title: 'Ticket médio', value: 'R$ 3.690,00', subtitle: 'Por venda realizada', icon: TrendingUp, color: 'purple' },
];

const history = [
  { date: '18/05/2024', proposal: '#1247', client: 'João Silva', value: 'R$ 110.000,00', commission: 'R$ 2.750,00', status: 'Pendente', statusColor: 'warning' },
  { date: '18/05/2024', proposal: '#1246', client: 'Maria Oliveira', value: 'R$ 72.000,00', commission: 'R$ 1.800,00', status: 'Pendente', statusColor: 'warning' },
  { date: '17/05/2024', proposal: '#1245', client: 'Carlos Santos', value: 'R$ 135.000,00', commission: 'R$ 3.375,00', status: 'Pendente', statusColor: 'warning' },
  { date: '16/05/2024', proposal: '#1244', client: 'Ana Paula', value: 'R$ 115.000,00', commission: 'R$ 2.875,00', status: 'Paga', statusColor: 'success' },
  { date: '15/05/2024', proposal: '#1243', client: 'Rafael Ferreira', value: 'R$ 98.000,00', commission: 'R$ 2.450,00', status: 'Paga', statusColor: 'success' },
];
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
        <a href="#" class="view-all">Ver todas</a>
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
            <tr v-for="(item, index) in history" :key="index">
              <td class="text-muted">{{ item.date }}</td>
              <td>
                <span class="proposal-id">{{ item.proposal }}</span>
              </td>
              <td class="font-medium">{{ item.client }}</td>
              <td>{{ item.value }}</td>
              <td class="font-medium">{{ item.commission }}</td>
              <td>
                <span class="badge" :class="item.statusColor">{{ item.status }}</span>
              </td>
            </tr>
          </tbody>
        </table>
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
}

.view-all:hover {
  text-decoration: underline;
}

.data-table {
  width: 100%;
  border-collapse: collapse;
}

.data-table th {
  text-align: left;
  padding: 12px 24px;
  font-size: 0.75rem;
  font-weight: 600;
  color: var(--text-muted);
  text-transform: uppercase;
  border-bottom: 1px solid var(--border);
}

.data-table td {
  padding: 16px 24px;
  border-bottom: 1px solid var(--border);
  font-size: 0.9rem;
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

@media (max-width: 768px) {
  .page-header {
    flex-direction: column;
    align-items: flex-start;
    gap: 16px;
  }
}
</style>
