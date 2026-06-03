<script setup>
import { ref, inject, computed } from 'vue';
import Modal from '../components/Modal.vue';
import Dropdown from '../components/Dropdown.vue';
import { Search, ChevronDown, Plus, ChevronLeft, ChevronRight } from '@lucide/vue';

const inventoryData = [
  { id: 1, name: 'Toyota Corolla XEi 2.0', plate: 'FGH-2A45', type: 'Carro', typeColor: 'purple', year: '2023', value: 'R$ 110.000,00', status: 'Disponível', img: 'car1' },
  { id: 2, name: 'Honda Civic Touring', plate: 'ABC-1234', type: 'Carro', typeColor: 'purple', year: '2022', value: 'R$ 145.000,00', status: 'Em negociação', img: 'car2' },
  { id: 3, name: 'Honda CB 500F', plate: 'JKL-3021', type: 'Moto', typeColor: 'yellow', year: '2023', value: 'R$ 35.900,00', status: 'Disponível', img: 'moto1' },
  { id: 4, name: 'Mercedes-Benz Accelo 1016', plate: 'ASD-9F87', type: 'Caminhão', typeColor: 'blue', year: '2021', value: 'R$ 160.000,00', status: 'Disponível', img: 'truck1' },
  { id: 5, name: 'Yamaha MT-07', plate: 'XYZ-9876', type: 'Moto', typeColor: 'yellow', year: '2024', value: 'R$ 45.000,00', status: 'Vendido', img: 'moto2' },
  { id: 6, name: 'Volkswagen T-Cross', plate: 'QWE-4567', type: 'Carro', typeColor: 'purple', year: '2021', value: 'R$ 105.000,00', status: 'Disponível', img: 'car3' },
];

const inventory = ref([...inventoryData]);

const showVehicleModal = ref(false);
const showInfoModal = ref(false);
const selectedVehicle = ref(null);
const showToast = inject('showToast');

const submitVehicle = () => {
  showVehicleModal.value = false;
  if (showToast) {
    showToast('Veículo adicionado com sucesso!', 'success');
  }
};

const openVehicleInfo = (item) => {
  selectedVehicle.value = item;
  showInfoModal.value = true;
};

const handleEdit = (item) => {
  if (showToast) showToast(`Editar veículo ${item.name}`, 'info');
};

const handleDelete = (item) => {
  if (showToast) showToast(`Veículo ${item.name} excluído!`, 'error');
  inventory.value = inventory.value.filter(v => v.id !== item.id);
};

// Search and Pagination Logic
const searchQuery = ref('');
const currentPage = ref(1);
const itemsPerPage = 4;

const filteredItems = computed(() => {
  if (!searchQuery.value) return inventory.value;
  const q = searchQuery.value.toLowerCase();
  return inventory.value.filter(item => 
    item.name.toLowerCase().includes(q) || 
    item.plate.toLowerCase().includes(q)
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
  <div class="inventory">
    <header class="page-header">
      <div>
        <h1 class="page-title">Estoque de veículos</h1>
        <p class="page-subtitle">Gerencie os veículos disponíveis para venda.</p>
      </div>
      <button class="btn-primary" @click="showVehicleModal = true">
        <Plus size="18" />
        Adicionar veículo
      </button>
    </header>

    <div class="card data-card">
      <div class="data-toolbar">
        <div class="search-box">
          <Search size="18" class="search-icon" />
          <input type="text" v-model="searchQuery" placeholder="Buscar por veículo ou placa..." @input="currentPage = 1" />
        </div>
        <div class="filter-actions">
          <div class="dropdown">
            <span>Todos os tipos</span>
            <ChevronDown size="16" />
          </div>
          <div class="dropdown">
            <span>Status</span>
            <ChevronDown size="16" />
          </div>
        </div>
      </div>

      <div class="table-responsive">
        <table class="data-table">
          <thead>
            <tr>
              <th>Veículo</th>
              <th>Placa</th>
              <th>Tipo</th>
              <th>Ano</th>
              <th>Valor</th>
              <th>Status</th>
              <th></th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="item in paginatedItems" :key="item.id" @click="openVehicleInfo(item)" class="clickable-row">
              <td>
                <div class="vehicle-info">
                  <div class="vehicle-img-placeholder">
                    <Car size="20" class="text-muted" />
                  </div>
                  <span class="font-medium">{{ item.name }}</span>
                </div>
              </td>
              <td><span class="plate-badge">{{ item.plate }}</span></td>
              <td>
                <span :class="`badge badge-${item.typeColor}`">{{ item.type }}</span>
              </td>
              <td class="text-muted">{{ item.year }}</td>
              <td class="font-medium">{{ item.value }}</td>
              <td>
                <span class="status-dot" :class="{'available': item.status === 'Disponível', 'negotiating': item.status === 'Em negociação'}"></span>
                {{ item.status }}
              </td>
              <td class="actions-col" @click.stop>
                <Dropdown :item="item" @edit="handleEdit" @delete="handleDelete" />
              </td>
            </tr>
            <tr v-if="paginatedItems.length === 0">
              <td colspan="7" class="text-center" style="padding: 32px; color: var(--text-muted);">
                Nenhum veículo encontrado para a busca "{{ searchQuery }}".
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

    <!-- Modal Adicionar Veículo -->
    <Modal :show="showVehicleModal" title="Adicionar Veículo" @close="showVehicleModal = false">
      <form @submit.prevent="submitVehicle" style="display: flex; flex-direction: column; gap: 16px;">
        <div style="display: flex; flex-direction: column; gap: 8px;">
          <label style="font-size: 0.85rem; font-weight: 500;">Nome do Veículo</label>
          <input type="text" placeholder="Ex: Honda Civic Touring" style="width: 100%; padding: 10px; border: 1px solid var(--border); border-radius: var(--radius-md); font-family: var(--font-family);" required />
        </div>
        <div style="display: flex; gap: 16px;">
          <div style="display: flex; flex-direction: column; gap: 8px; flex: 1;">
            <label style="font-size: 0.85rem; font-weight: 500;">Tipo</label>
            <select style="width: 100%; padding: 10px; border: 1px solid var(--border); border-radius: var(--radius-md); font-family: var(--font-family);">
              <option>Carro</option>
              <option>Moto</option>
              <option>Caminhão</option>
            </select>
          </div>
          <div style="display: flex; flex-direction: column; gap: 8px; flex: 1;">
            <label style="font-size: 0.85rem; font-weight: 500;">Ano</label>
            <input type="text" placeholder="Ex: 2024" style="width: 100%; padding: 10px; border: 1px solid var(--border); border-radius: var(--radius-md); font-family: var(--font-family);" required />
          </div>
        </div>
        <div style="display: flex; flex-direction: column; gap: 8px;">
          <label style="font-size: 0.85rem; font-weight: 500;">Valor (R$)</label>
          <input type="text" placeholder="Ex: 145.000,00" style="width: 100%; padding: 10px; border: 1px solid var(--border); border-radius: var(--radius-md); font-family: var(--font-family);" required />
        </div>
        <div style="display: flex; justify-content: flex-end; gap: 12px; margin-top: 16px;">
          <button type="button" @click="showVehicleModal = false" style="padding: 10px 16px; border: 1px solid var(--border); border-radius: var(--radius-md); background: white; cursor: pointer;">Cancelar</button>
          <button type="submit" class="btn-primary">Adicionar Veículo</button>
        </div>
      </form>
    </Modal>

    <!-- Modal Detalhes do Veículo -->
    <Modal :show="showInfoModal" title="Detalhes do Veículo" @close="showInfoModal = false">
      <div v-if="selectedVehicle" class="vehicle-details">
        <div class="vehicle-image-hero">
          <img src="https://images.unsplash.com/photo-1494976388531-d1058494cdd8?auto=format&fit=crop&q=80&w=800" alt="Vehicle Photo" class="hero-img" v-if="selectedVehicle.type === 'Carro'" />
          <img src="https://images.unsplash.com/photo-1558981403-c5f9899a289f?auto=format&fit=crop&q=80&w=800" alt="Motorcycle Photo" class="hero-img" v-else-if="selectedVehicle.type === 'Moto'" />
          <img src="https://images.unsplash.com/photo-1601584115197-04ecc0da31d7?auto=format&fit=crop&q=80&w=800" alt="Truck Photo" class="hero-img" v-else />
          <div class="hero-overlay">
            <span :class="`badge badge-${selectedVehicle.typeColor}`">{{ selectedVehicle.type }}</span>
            <span class="plate-badge-large">{{ selectedVehicle.plate }}</span>
          </div>
        </div>
        
        <div class="vehicle-info-grid">
          <div class="info-main">
            <h2 class="vehicle-name-large">{{ selectedVehicle.name }}</h2>
            <p class="vehicle-year-large">Ano {{ selectedVehicle.year }}</p>
          </div>
          <div class="info-price">
            <span class="price-label">Valor de Venda</span>
            <span class="price-value">{{ selectedVehicle.value }}</span>
          </div>
        </div>

        <div class="vehicle-specs">
          <div class="spec-item">
            <span class="spec-label">Status</span>
            <span class="spec-value">
              <span class="status-dot" :class="{'available': selectedVehicle.status === 'Disponível', 'negotiating': selectedVehicle.status === 'Em negociação'}"></span>
              {{ selectedVehicle.status }}
            </span>
          </div>
          <div class="spec-item">
            <span class="spec-label">ID Sistema</span>
            <span class="spec-value">#{{ selectedVehicle.id }}</span>
          </div>
          <div class="spec-item">
            <span class="spec-label">Cadastrado</span>
            <span class="spec-value">12/05/2024</span>
          </div>
        </div>

        <div class="modal-actions">
          <button class="btn-outline" @click="showInfoModal = false">Fechar</button>
          <button class="btn-primary" style="flex: 1;" @click="showInfoModal = false">Iniciar Negociação</button>
        </div>
      </div>
    </Modal>
  </div>
</template>

<script>
import { Car } from '@lucide/vue';
export default {
  components: { Car }
}
</script>

<style scoped>
.inventory {
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
  overflow: visible; /* Fix dropdown clipping */
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

.vehicle-info {
  display: flex;
  align-items: center;
  gap: 12px;
}

.vehicle-img-placeholder {
  width: 48px;
  height: 36px;
  background-color: var(--bg-color);
  border-radius: var(--radius-md);
  display: flex;
  align-items: center;
  justify-content: center;
}

.plate-badge {
  background-color: var(--bg-color);
  padding: 4px 8px;
  border-radius: 4px;
  font-family: monospace;
  font-size: 0.85rem;
  border: 1px solid var(--border);
  font-weight: 500;
}

.status-dot {
  display: inline-block;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  margin-right: 8px;
  background-color: var(--text-muted);
}

.status-dot.available { background-color: var(--success); }
.status-dot.negotiating { background-color: var(--warning); }

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

.clickable-row {
  cursor: pointer;
}

.clickable-row:hover td {
  background-color: #F8FAFC !important;
}

.vehicle-details {
  display: flex;
  flex-direction: column;
  gap: 20px;
  margin-top: 8px;
}

.vehicle-image-hero {
  position: relative;
  width: 100%;
  height: 200px;
  border-radius: var(--radius-lg);
  overflow: hidden;
}

.hero-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.hero-overlay {
  position: absolute;
  top: 16px;
  left: 16px;
  right: 16px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.plate-badge-large {
  background-color: rgba(255, 255, 255, 0.9);
  padding: 6px 12px;
  border-radius: 6px;
  font-family: monospace;
  font-size: 0.95rem;
  font-weight: 600;
  color: #1A1C23;
  box-shadow: var(--shadow-sm);
}

.vehicle-info-grid {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
  padding-bottom: 16px;
  border-bottom: 1px solid var(--border);
}

.info-main {
  display: flex;
  flex-direction: column;
}

.vehicle-name-large {
  font-size: 1.25rem;
  font-weight: 700;
  color: var(--text-main);
  margin-bottom: 4px;
}

.vehicle-year-large {
  color: var(--text-muted);
  font-size: 0.9rem;
}

.info-price {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
}

.price-label {
  font-size: 0.75rem;
  color: var(--text-muted);
  text-transform: uppercase;
  font-weight: 600;
  margin-bottom: 4px;
}

.price-value {
  font-size: 1.5rem;
  font-weight: 700;
  color: var(--primary);
}

.vehicle-specs {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 16px;
  padding: 8px 0;
}

.spec-item {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.spec-label {
  font-size: 0.8rem;
  color: var(--text-muted);
}

.spec-value {
  font-size: 0.95rem;
  font-weight: 500;
  color: var(--text-main);
  display: flex;
  align-items: center;
}

.modal-actions {
  display: flex;
  gap: 12px;
  margin-top: 16px;
}

.btn-outline {
  padding: 12px 24px;
  border: 1px solid var(--border);
  background: white;
  border-radius: var(--radius-md);
  font-weight: 500;
  cursor: pointer;
  transition: all 0.2s;
}

.btn-outline:hover {
  background-color: var(--bg-color);
}
</style>
