<script setup>
import { ref, computed, watch } from 'vue';
import Modal from './Modal.vue';
import { ChevronRight, ChevronLeft, Car, FileText, User, CheckCircle, Check } from '@lucide/vue';

const props = defineProps({
  show: {
    type: Boolean,
    required: true
  }
});

const emit = defineEmits(['close', 'submit']);

// State
const currentStep = ref(1);
const selectedCar = ref(null);
const selectedLetter = ref(null);
const selectedClient = ref(null);

// Mocks
const cars = ref([
  { id: 1, name: 'Toyota Corolla XEi 2.0', value: 110000, valueFormatted: 'R$ 110.000,00', year: 2023, image: 'https://via.placeholder.com/80?text=Corolla' },
  { id: 2, name: 'Hyundai HB20 Comfort', value: 72000, valueFormatted: 'R$ 72.000,00', year: 2024, image: 'https://via.placeholder.com/80?text=HB20' },
  { id: 3, name: 'Jeep Compass Limited', value: 135000, valueFormatted: 'R$ 135.000,00', year: 2022, image: 'https://via.placeholder.com/80?text=Compass' },
  { id: 4, name: 'Fiat Toro Volcano', value: 115000, valueFormatted: 'R$ 115.000,00', year: 2022, image: 'https://via.placeholder.com/80?text=Toro' },
]);

const letters = ref([
  { id: 1, value: 115000, valueFormatted: 'R$ 115.000,00', parcel: 'R$ 1.250,00', adminFee: '15%', prazo: '120 meses' },
  { id: 2, value: 110000, valueFormatted: 'R$ 110.000,00', parcel: 'R$ 1.180,00', adminFee: '14%', prazo: '120 meses' },
  { id: 3, value: 120000, valueFormatted: 'R$ 120.000,00', parcel: 'R$ 1.350,00', adminFee: '16%', prazo: '100 meses' },
  { id: 4, value: 75000, valueFormatted: 'R$ 75.000,00', parcel: 'R$ 890,00', adminFee: '12%', prazo: '100 meses' },
  { id: 5, value: 140000, valueFormatted: 'R$ 140.000,00', parcel: 'R$ 1.550,00', adminFee: '15%', prazo: '120 meses' },
]);

const clients = ref([
  { id: 1, name: 'João Silva', doc: '111.222.333-44', phone: '(11) 90000-1111' },
  { id: 2, name: 'Maria Oliveira', doc: '222.333.444-55', phone: '(11) 91111-2222' },
  { id: 3, name: 'Carlos Santos', doc: '333.444.555-66', phone: '(11) 92222-3333' },
  { id: 4, name: 'Ana Paula', doc: '444.555.666-77', phone: '(11) 96666-4444' },
]);

// Computed
const availableLetters = computed(() => {
  if (!selectedCar.value) return [];
  // Retorna cartas que têm valor igual ou um pouco maior que o carro
  return letters.value.filter(l => l.value >= selectedCar.value.value).sort((a, b) => a.value - b.value).slice(0, 3);
});

const canProceed = computed(() => {
  if (currentStep.value === 1) return !!selectedCar.value;
  if (currentStep.value === 2) return !!selectedLetter.value;
  if (currentStep.value === 3) return !!selectedClient.value;
  return true;
});

// Methods
const nextStep = () => {
  if (currentStep.value < 4 && canProceed.value) {
    currentStep.value++;
  }
};

const prevStep = () => {
  if (currentStep.value > 1) {
    currentStep.value--;
  }
};

const handleSubmit = () => {
  const proposalData = {
    car: selectedCar.value,
    letter: selectedLetter.value,
    client: selectedClient.value,
  };
  emit('submit', proposalData);
  resetModal();
};

const handleClose = () => {
  emit('close');
  // Optional: reset on close, depending on desired UX
};

const resetModal = () => {
  currentStep.value = 1;
  selectedCar.value = null;
  selectedLetter.value = null;
  selectedClient.value = null;
};

// Reset state when modal is opened to ensure clean state
watch(() => props.show, (newVal) => {
  if (newVal) {
    resetModal();
  }
});
</script>

<template>
  <Modal :show="show" title="Nova Proposta" @close="handleClose">
    <div class="wizard-container">
      
      <!-- Stepper Header -->
      <div class="stepper">
        <div class="step" :class="{ active: currentStep >= 1, completed: currentStep > 1 }">
          <div class="step-icon"><Car size="16" /></div>
          <span class="step-label">Veículo</span>
        </div>
        <div class="step-line" :class="{ active: currentStep >= 2 }"></div>
        
        <div class="step" :class="{ active: currentStep >= 2, completed: currentStep > 2 }">
          <div class="step-icon"><FileText size="16" /></div>
          <span class="step-label">Carta</span>
        </div>
        <div class="step-line" :class="{ active: currentStep >= 3 }"></div>
        
        <div class="step" :class="{ active: currentStep >= 3, completed: currentStep > 3 }">
          <div class="step-icon"><User size="16" /></div>
          <span class="step-label">Cliente</span>
        </div>
        <div class="step-line" :class="{ active: currentStep >= 4 }"></div>
        
        <div class="step" :class="{ active: currentStep >= 4 }">
          <div class="step-icon"><CheckCircle size="16" /></div>
          <span class="step-label">Resumo</span>
        </div>
      </div>

      <!-- Step Content -->
      <div class="step-content">
        
        <!-- Passo 1: Veículo -->
        <div v-if="currentStep === 1" class="step-pane">
          <h3 class="pane-title">Selecione o veículo de interesse</h3>
          <p class="pane-subtitle">Escolha o veículo que o cliente deseja adquirir.</p>
          
          <div class="selection-grid">
            <div 
              v-for="car in cars" 
              :key="car.id" 
              class="selection-card" 
              :class="{ selected: selectedCar?.id === car.id }"
              @click="selectedCar = car"
            >
              <div class="card-check" v-if="selectedCar?.id === car.id"><Check size="14" /></div>
              <div class="car-info">
                <span class="car-name">{{ car.name }}</span>
                <span class="car-year">Ano {{ car.year }}</span>
                <span class="car-value">{{ car.valueFormatted }}</span>
              </div>
            </div>
          </div>
        </div>

        <!-- Passo 2: Carta -->
        <div v-if="currentStep === 2" class="step-pane">
          <h3 class="pane-title">Cartas disponíveis</h3>
          <p class="pane-subtitle">Opções compatíveis com o valor do veículo ({{ selectedCar?.valueFormatted }}).</p>
          
          <div class="selection-grid letters-grid">
            <div 
              v-for="letter in availableLetters" 
              :key="letter.id" 
              class="selection-card letter-card" 
              :class="{ selected: selectedLetter?.id === letter.id }"
              @click="selectedLetter = letter"
            >
              <div class="card-check" v-if="selectedLetter?.id === letter.id"><Check size="14" /></div>
              <div class="letter-header">
                <span class="letter-value">{{ letter.valueFormatted }}</span>
                <span class="badge badge-success">Disponível</span>
              </div>
              <div class="letter-details">
                <div class="detail-row">
                  <span>Parcela:</span>
                  <strong>{{ letter.parcel }}</strong>
                </div>
                <div class="detail-row">
                  <span>Prazo:</span>
                  <strong>{{ letter.prazo }}</strong>
                </div>
                <div class="detail-row">
                  <span>Taxa Admin:</span>
                  <strong>{{ letter.adminFee }}</strong>
                </div>
              </div>
            </div>
            
            <div v-if="availableLetters.length === 0" class="empty-state">
              Nenhuma carta disponível para este valor no momento.
            </div>
          </div>
        </div>

        <!-- Passo 3: Cliente -->
        <div v-if="currentStep === 3" class="step-pane">
          <h3 class="pane-title">Selecione o Cliente</h3>
          <p class="pane-subtitle">Para quem será enviada esta proposta?</p>
          
          <div class="selection-list">
            <div 
              v-for="client in clients" 
              :key="client.id" 
              class="list-item" 
              :class="{ selected: selectedClient?.id === client.id }"
              @click="selectedClient = client"
            >
              <div class="client-avatar">{{ client.name.substring(0, 2).toUpperCase() }}</div>
              <div class="client-info">
                <span class="client-name">{{ client.name }}</span>
                <span class="client-doc">{{ client.doc }} • {{ client.phone }}</span>
              </div>
              <div class="radio-circle">
                <div class="radio-inner" v-if="selectedClient?.id === client.id"></div>
              </div>
            </div>
          </div>
        </div>

        <!-- Passo 4: Resumo -->
        <div v-if="currentStep === 4" class="step-pane">
          <h3 class="pane-title">Resumo da Proposta</h3>
          <p class="pane-subtitle">Confira os dados antes de gerar a proposta.</p>
          
          <div class="summary-box">
            <div class="summary-section">
              <h4>Cliente</h4>
              <p><strong>{{ selectedClient?.name }}</strong></p>
              <p>{{ selectedClient?.doc }} • {{ selectedClient?.phone }}</p>
            </div>
            
            <div class="summary-section">
              <h4>Veículo</h4>
              <p><strong>{{ selectedCar?.name }} ({{ selectedCar?.year }})</strong></p>
              <p>Valor: {{ selectedCar?.valueFormatted }}</p>
            </div>
            
            <div class="summary-section">
              <h4>Carta de Consórcio</h4>
              <p><strong>Crédito: {{ selectedLetter?.valueFormatted }}</strong></p>
              <p>Parcela: {{ selectedLetter?.parcel }} • Prazo: {{ selectedLetter?.prazo }}</p>
            </div>
          </div>
        </div>

      </div>

      <!-- Footer Actions -->
      <div class="wizard-footer">
        <button v-if="currentStep > 1" type="button" class="btn-outline" @click="prevStep">
          <ChevronLeft size="16" />
          Voltar
        </button>
        <div v-else></div> <!-- Spacer -->
        
        <button v-if="currentStep < 4" type="button" class="btn-primary" @click="nextStep" :disabled="!canProceed">
          Próximo Passo
          <ChevronRight size="16" />
        </button>
        
        <button v-if="currentStep === 4" type="button" class="btn-primary" @click="handleSubmit">
          <CheckCircle size="16" />
          Gerar Proposta
        </button>
      </div>
      
    </div>
  </Modal>
</template>

<style scoped>
.wizard-container {
  display: flex;
  flex-direction: column;
  gap: 24px;
}

/* Stepper */
.stepper {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 0 16px;
  margin-bottom: 8px;
}

.step {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 8px;
  position: relative;
  z-index: 2;
}

.step-icon {
  width: 32px;
  height: 32px;
  border-radius: 50%;
  background-color: var(--surface);
  border: 2px solid var(--border);
  display: flex;
  align-items: center;
  justify-content: center;
  color: var(--text-muted);
  transition: all 0.3s;
}

.step.active .step-icon {
  border-color: var(--primary);
  color: var(--primary);
}

.step.completed .step-icon {
  background-color: var(--primary);
  color: white;
  border-color: var(--primary);
}

.step-label {
  font-size: 0.75rem;
  font-weight: 600;
  color: var(--text-muted);
  transition: all 0.3s;
}

.step.active .step-label {
  color: var(--primary);
}

.step-line {
  flex: 1;
  height: 2px;
  background-color: var(--border);
  margin: 0 8px;
  position: relative;
  top: -10px;
  transition: all 0.3s;
}

.step-line.active {
  background-color: var(--primary);
}

/* Content */
.step-content {
  min-height: 250px;
}

.pane-title {
  font-size: 1.1rem;
  font-weight: 600;
  margin-bottom: 4px;
}

.pane-subtitle {
  font-size: 0.85rem;
  color: var(--text-muted);
  margin-bottom: 16px;
}

/* Grids */
.selection-grid {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 12px;
}

.letters-grid {
  grid-template-columns: 1fr;
}

.selection-card {
  border: 2px solid var(--border);
  border-radius: var(--radius-lg);
  padding: 16px;
  cursor: pointer;
  position: relative;
  transition: all 0.2s;
  background-color: white;
}

.selection-card:hover {
  border-color: var(--primary-light);
}

.selection-card.selected {
  border-color: var(--primary);
  background-color: rgba(124, 58, 237, 0.05); /* Primary tint */
}

.card-check {
  position: absolute;
  top: 12px;
  right: 12px;
  width: 20px;
  height: 20px;
  background-color: var(--primary);
  color: white;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
}

.car-info {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.car-name {
  font-weight: 600;
  font-size: 0.95rem;
}

.car-year {
  font-size: 0.8rem;
  color: var(--text-muted);
}

.car-value {
  font-weight: 700;
  color: var(--text-main);
  margin-top: 4px;
}

/* Letter Card */
.letter-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 12px;
}

.letter-value {
  font-size: 1.1rem;
  font-weight: 700;
}

.letter-details {
  display: flex;
  flex-direction: column;
  gap: 8px;
  font-size: 0.85rem;
}

.detail-row {
  display: flex;
  justify-content: space-between;
  color: var(--text-muted);
}

.detail-row strong {
  color: var(--text-main);
}

/* Lists */
.selection-list {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.list-item {
  display: flex;
  align-items: center;
  padding: 12px 16px;
  border: 1px solid var(--border);
  border-radius: var(--radius-lg);
  cursor: pointer;
  transition: all 0.2s;
}

.list-item:hover {
  border-color: var(--primary-light);
}

.list-item.selected {
  border-color: var(--primary);
  background-color: rgba(124, 58, 237, 0.05);
}

.client-avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: var(--primary-light);
  color: var(--primary);
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
  font-size: 0.9rem;
  margin-right: 12px;
}

.client-info {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.client-name {
  font-weight: 600;
  font-size: 0.95rem;
}

.client-doc {
  font-size: 0.8rem;
  color: var(--text-muted);
}

.radio-circle {
  width: 20px;
  height: 20px;
  border-radius: 50%;
  border: 2px solid var(--border);
  display: flex;
  align-items: center;
  justify-content: center;
}

.list-item.selected .radio-circle {
  border-color: var(--primary);
}

.radio-inner {
  width: 10px;
  height: 10px;
  border-radius: 50%;
  background-color: var(--primary);
}

/* Summary */
.summary-box {
  background-color: var(--bg-color);
  border-radius: var(--radius-lg);
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.summary-section {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.summary-section h4 {
  font-size: 0.8rem;
  text-transform: uppercase;
  color: var(--text-muted);
  letter-spacing: 0.5px;
  margin-bottom: 4px;
}

.summary-section p {
  font-size: 0.9rem;
  color: var(--text-main);
  margin: 0;
}

.wizard-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-top: 16px;
  border-top: 1px solid var(--border);
}

.btn-outline {
  display: inline-flex;
  align-items: center;
  gap: 8px;
  padding: 10px 16px;
  background-color: transparent;
  border: 1px solid var(--border);
  color: var(--text-main);
  border-radius: var(--radius-md);
  font-weight: 600;
  font-size: 0.9rem;
  cursor: pointer;
  transition: all 0.2s;
}

.btn-outline:hover {
  background-color: var(--bg-color);
}

.btn-primary:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

@media (max-width: 640px) {
  .selection-grid {
    grid-template-columns: 1fr;
  }
}
</style>
