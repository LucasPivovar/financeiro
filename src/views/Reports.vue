<script setup>
import { 
  ShoppingCart, 
  FileText, 
  CheckSquare, 
  DollarSign, 
  Download, 
  ChevronDown 
} from '@lucide/vue';
import { 
  Chart as ChartJS, 
  CategoryScale, 
  LinearScale, 
  PointElement, 
  LineElement, 
  Title, 
  Tooltip, 
  Legend, 
  Filler,
  ArcElement
} from 'chart.js';
import { Line, Doughnut } from 'vue-chartjs';

ChartJS.register(
  CategoryScale, 
  LinearScale, 
  PointElement, 
  LineElement, 
  Title, 
  Tooltip, 
  Legend, 
  Filler,
  ArcElement
);

const summaryCards = [
  { title: 'Vendas realizadas', value: '18', change: '+ 28% vs mês anterior', changeType: 'positive', icon: ShoppingCart, color: 'purple' },
  { title: 'Propostas enviadas', value: '43', change: '+ 12% vs mês anterior', changeType: 'positive', icon: FileText, color: 'blue' },
  { title: 'Taxa de aprovação', value: '62%', change: '+ 8% vs mês anterior', changeType: 'positive', icon: CheckSquare, color: 'green' },
  { title: 'Comissões geradas', value: 'R$ 18.450,00', change: '+ 32% vs mês anterior', changeType: 'positive', icon: DollarSign, color: 'yellow' },
];

const lineChartData = {
  labels: ['01/05', '05/05', '09/05', '13/05', '17/05', '21/05', '25/05', '29/05'],
  datasets: [
    {
      label: 'Vendas',
      data: [4, 11, 15, 8, 4, 20, 11, 21, 14],
      borderColor: '#6322F2',
      backgroundColor: (context) => {
        const ctx = context.chart.ctx;
        const gradient = ctx.createLinearGradient(0, 0, 0, 300);
        gradient.addColorStop(0, 'rgba(99, 34, 242, 0.2)');
        gradient.addColorStop(1, 'rgba(99, 34, 242, 0)');
        return gradient;
      },
      borderWidth: 2,
      fill: true,
      tension: 0.4,
      pointBackgroundColor: '#6322F2',
      pointBorderColor: '#fff',
      pointBorderWidth: 2,
      pointRadius: 0,
      pointHoverRadius: 6,
    }
  ]
};

const lineChartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      display: false
    },
    tooltip: {
      backgroundColor: '#1A1C23',
      padding: 12,
      titleFont: { family: 'Inter', size: 13 },
      bodyFont: { family: 'Inter', size: 14, weight: 'bold' },
      displayColors: false,
      callbacks: {
        label: (context) => `${context.raw} vendas`
      }
    }
  },
  scales: {
    y: {
      beginAtZero: true,
      max: 25,
      border: { display: false },
      grid: {
        color: '#E5E7EB',
        drawBorder: false,
      },
      ticks: {
        color: '#6B7280',
        font: { family: 'Inter', size: 12 },
        stepSize: 5
      }
    },
    x: {
      border: { display: false },
      grid: { display: false },
      ticks: {
        color: '#6B7280',
        font: { family: 'Inter', size: 12 }
      }
    }
  }
};

const donutChartData = {
  labels: ['Carros', 'Motos', 'Caminhões'],
  datasets: [
    {
      data: [75, 15, 10],
      backgroundColor: ['#6322F2', '#F59E0B', '#3B82F6'],
      hoverBackgroundColor: ['#501AC3', '#D97706', '#2563EB'],
      borderWidth: 0,
      cutout: '70%',
    }
  ]
};

const donutChartOptions = {
  responsive: true,
  maintainAspectRatio: false,
  plugins: {
    legend: {
      display: false
    },
    tooltip: {
      backgroundColor: '#1A1C23',
      padding: 12,
      titleFont: { family: 'Inter', size: 13 },
      bodyFont: { family: 'Inter', size: 14, weight: 'bold' },
      callbacks: {
        label: (context) => ` ${context.raw}%`
      }
    }
  }
};
</script>

<template>
  <div class="reports">
    <header class="page-header">
      <div>
        <h1 class="page-title">Relatórios</h1>
        <p class="page-subtitle">Analise os dados do seu negócio.</p>
      </div>
      <div class="header-actions">
        <div class="dropdown">
          <span>Este mês</span>
          <ChevronDown size="16" />
        </div>
        <button class="btn-outline">
          <Download size="18" />
          Exportar
        </button>
      </div>
    </header>

    <div class="summary-grid">
      <div v-for="(card, index) in summaryCards" :key="index" class="card summary-card">
        <div class="card-icon" :class="card.color">
          <component :is="card.icon" size="24" />
        </div>
        <div class="card-content">
          <h3 class="card-title">{{ card.title }}</h3>
          <div class="card-value">{{ card.value }}</div>
          <div v-if="card.change" class="card-change" :class="card.changeType">
            <span class="trend-icon">↑</span>
            {{ card.change }}
          </div>
        </div>
      </div>
    </div>

    <div class="charts-grid">
      <div class="card chart-card line-chart-container">
        <h2 class="section-title">Vendas por período</h2>
        <div class="chart-wrapper">
          <Line :data="lineChartData" :options="lineChartOptions" />
        </div>
      </div>

      <div class="card chart-card donut-chart-container">
        <h2 class="section-title">Vendas por tipo de veículo</h2>
        <div class="donut-content">
          <div class="donut-wrapper">
            <Doughnut :data="donutChartData" :options="donutChartOptions" />
          </div>
          <div class="donut-legend">
            <div class="legend-item">
              <div class="legend-color" style="background-color: #6322F2;"></div>
              <span class="legend-label">Carros</span>
              <span class="legend-value">75%</span>
            </div>
            <div class="legend-item">
              <div class="legend-color" style="background-color: #F59E0B;"></div>
              <span class="legend-label">Motos</span>
              <span class="legend-value">15%</span>
            </div>
            <div class="legend-item">
              <div class="legend-color" style="background-color: #3B82F6;"></div>
              <span class="legend-label">Caminhões</span>
              <span class="legend-value">10%</span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.reports {
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

.header-actions {
  display: flex;
  gap: 16px;
  align-items: center;
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

.btn-outline {
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
  transition: all 0.2s;
}

.btn-outline:hover {
  background-color: var(--bg-color);
  border-color: var(--text-muted);
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

.card-change {
  display: inline-flex;
  align-items: center;
  gap: 4px;
  font-size: 0.75rem;
  font-weight: 600;
}

.card-change.positive { color: var(--success); }
.trend-icon { font-size: 0.9rem; }

.charts-grid {
  display: grid;
  grid-template-columns: 2fr 1fr;
  gap: 24px;
}

.chart-card {
  padding: 24px;
  display: flex;
  flex-direction: column;
}

.section-title {
  font-size: 1.1rem;
  font-weight: 600;
  margin-bottom: 24px;
}

.chart-wrapper {
  position: relative;
  height: 300px;
  width: 100%;
}

.donut-content {
  display: flex;
  flex-direction: column;
  align-items: center;
  flex: 1;
  justify-content: center;
  gap: 32px;
}

.donut-wrapper {
  position: relative;
  height: 200px;
  width: 200px;
}

.donut-legend {
  display: flex;
  flex-direction: column;
  gap: 12px;
  width: 100%;
}

.legend-item {
  display: flex;
  align-items: center;
  font-size: 0.9rem;
}

.legend-color {
  width: 12px;
  height: 12px;
  border-radius: 4px;
  margin-right: 12px;
}

.legend-label {
  color: var(--text-main);
  font-weight: 500;
  flex: 1;
}

.legend-value {
  font-weight: 600;
  color: var(--text-main);
}

@media (max-width: 1024px) {
  .charts-grid {
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
</style>
