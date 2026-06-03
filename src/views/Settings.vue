<script setup>
import { 
  User, 
  Building, 
  Bell, 
  ShieldCheck, 
  PenTool, 
  Link,
  CreditCard
} from '@lucide/vue';
import { ref, inject } from 'vue';

const showToast = inject('showToast');
const activeTab = ref('profile');

const tabs = [
  { id: 'profile', name: 'Perfil da Loja', icon: Building },
  { id: 'bank', name: 'Dados Bancários', icon: CreditCard },
  { id: 'users', name: 'Usuários e Permissões', icon: User },
  { id: 'notifications', name: 'Notificações', icon: Bell },
  { id: 'security', name: 'Segurança', icon: ShieldCheck },
  { id: 'appearance', name: 'Personalização', icon: PenTool },
  { id: 'integrations', name: 'Integrações', icon: Link },
];

const isLoading = ref(false);

const saveSettings = () => {
  isLoading.value = true;
  setTimeout(() => {
    isLoading.value = false;
    if (showToast) {
      showToast('Configurações salvas com sucesso!', 'success');
    }
  }, 1000);
};

// Mock data for Appearance
const primaryColor = ref('#6322F2');
const theme = ref('light');

// Mock data for Integrations
const integrations = ref([
  { id: 1, name: 'WhatsApp Business', desc: 'Envie propostas diretamente via WhatsApp.', status: true, icon: 'https://upload.wikimedia.org/wikipedia/commons/thumb/6/6b/WhatsApp.svg/512px-WhatsApp.svg.png' },
  { id: 2, name: 'RD Station', desc: 'Sincronize seus clientes e leads.', status: false, icon: 'https://www.rdstation.com/wp-content/uploads/2020/09/Logo-RD-Station-Default.png' },
  { id: 3, name: 'Asaas', desc: 'Gerencie recebimentos e emissão de boletos.', status: false, icon: 'https://logospng.org/download/asaas/asaas-icon-1024.png' }
]);
</script>

<template>
  <div class="settings">
    <header class="page-header">
      <div>
        <h1 class="page-title">Configurações</h1>
        <p class="page-subtitle">Gerencie as preferências da sua conta e da loja.</p>
      </div>
      <button class="btn-primary" @click="saveSettings" :disabled="isLoading">
        <span v-if="isLoading">Salvando...</span>
        <span v-else>Salvar alterações</span>
      </button>
    </header>

    <div class="settings-layout">
      <!-- Sidebar de Configurações -->
      <aside class="settings-nav card">
        <nav>
          <button 
            v-for="tab in tabs" 
            :key="tab.id"
            class="settings-tab"
            :class="{ active: activeTab === tab.id }"
            @click="activeTab = tab.id"
          >
            <component :is="tab.icon" size="18" />
            {{ tab.name }}
          </button>
        </nav>
      </aside>

      <!-- Conteúdo Principal -->
      <main class="settings-content card">
        
        <!-- Perfil da Loja -->
        <div v-if="activeTab === 'profile'" class="tab-pane fade-in">
          <h2 class="pane-title">Perfil da Loja</h2>
          <p class="pane-desc">Informações públicas que serão exibidas para seus clientes.</p>
          
          <div class="profile-pic-section">
            <div class="pic-placeholder">AM</div>
            <div>
              <button class="btn-outline" style="margin-bottom: 8px;">Alterar logotipo</button>
              <p class="text-xs text-muted">Recomendado: PNG ou JPG, 256x256px.</p>
            </div>
          </div>

          <div class="form-grid" style="margin-top: 24px;">
            <div class="form-group">
              <label>Nome da Loja</label>
              <input type="text" class="form-input" value="Auto Motors" />
            </div>
            <div class="form-group">
              <label>CNPJ</label>
              <input type="text" class="form-input" value="12.345.678/0001-90" disabled />
            </div>
            <div class="form-group full-width">
              <label>Endereço</label>
              <input type="text" class="form-input" value="Av. Paulista, 1000 - São Paulo, SP" />
            </div>
            <div class="form-group">
              <label>E-mail de Contato</label>
              <input type="email" class="form-input" value="contato@automotors.com.br" />
            </div>
            <div class="form-group">
              <label>Telefone</label>
              <input type="text" class="form-input" value="(11) 3000-4000" />
            </div>
          </div>
        </div>

        <!-- Dados Bancários -->
        <div v-if="activeTab === 'bank'" class="tab-pane fade-in">
          <h2 class="pane-title">Dados Bancários</h2>
          <p class="pane-desc">Contas para recebimento de comissões e repasses.</p>
          <div class="form-grid">
            <div class="form-group full-width">
              <label>Titular da Conta</label>
              <input type="text" class="form-input" value="Auto Motors Comércio de Veículos LTDA" />
            </div>
            <div class="form-group">
              <label>Banco</label>
              <select class="form-input">
                <option>Itaú Unibanco (341)</option>
                <option>Bradesco (237)</option>
                <option>Nubank (260)</option>
                <option>Inter (077)</option>
              </select>
            </div>
            <div class="form-group">
              <label>Tipo de Conta</label>
              <select class="form-input">
                <option>Conta Corrente</option>
                <option>Conta Poupança</option>
              </select>
            </div>
            <div class="form-group">
              <label>Agência</label>
              <input type="text" class="form-input" placeholder="Ex: 0001" />
            </div>
            <div class="form-group">
              <label>Conta com Dígito</label>
              <input type="text" class="form-input" placeholder="Ex: 12345-6" />
            </div>
            <div class="form-group full-width">
              <label>Chave PIX (Opcional)</label>
              <input type="text" class="form-input" value="12.345.678/0001-90" />
            </div>
          </div>
        </div>

        <!-- Usuários e Permissões -->
        <div v-if="activeTab === 'users'" class="tab-pane fade-in">
          <div style="display:flex; justify-content:space-between; align-items:center; margin-bottom: 24px;">
            <div>
              <h2 class="pane-title">Usuários da Equipe</h2>
              <p class="pane-desc" style="margin-bottom:0;">Gerencie quem tem acesso ao painel da sua loja.</p>
            </div>
            <button class="btn-primary" style="padding: 8px 16px;">+ Convidar Usuário</button>
          </div>
          
          <table class="data-table">
            <thead>
              <tr>
                <th>Nome</th>
                <th>E-mail</th>
                <th>Permissão</th>
                <th>Status</th>
                <th></th>
              </tr>
            </thead>
            <tbody>
              <tr>
                <td class="font-medium">João Admin</td>
                <td class="text-muted">joao@automotors.com.br</td>
                <td><span class="badge badge-primary">Administrador</span></td>
                <td><span style="color: var(--success); font-size: 0.85rem; font-weight: 500;">Ativo</span></td>
                <td><a href="#" style="color: var(--primary); font-size: 0.85rem; font-weight: 500;">Editar</a></td>
              </tr>
              <tr>
                <td class="font-medium">Vendedor 1</td>
                <td class="text-muted">vendas@automotors.com.br</td>
                <td><span class="badge badge-warning">Vendedor</span></td>
                <td><span style="color: var(--success); font-size: 0.85rem; font-weight: 500;">Ativo</span></td>
                <td><a href="#" style="color: var(--primary); font-size: 0.85rem; font-weight: 500;">Editar</a></td>
              </tr>
              <tr>
                <td class="font-medium">Vendedor 2</td>
                <td class="text-muted">vendas2@automotors.com.br</td>
                <td><span class="badge badge-warning">Vendedor</span></td>
                <td><span style="color: var(--text-muted); font-size: 0.85rem; font-weight: 500;">Pendente</span></td>
                <td><a href="#" style="color: var(--primary); font-size: 0.85rem; font-weight: 500;">Reenviar</a></td>
              </tr>
            </tbody>
          </table>
        </div>

        <!-- Notificações -->
        <div v-if="activeTab === 'notifications'" class="tab-pane fade-in">
          <h2 class="pane-title">Notificações</h2>
          <p class="pane-desc">Escolha como você deseja ser alertado.</p>
          <div class="toggle-group">
            <div class="toggle-item">
              <div>
                <h4>Novas Propostas</h4>
                <p>Receber e-mail quando um cliente enviar nova proposta.</p>
              </div>
              <label class="switch"><input type="checkbox" checked><span class="slider"></span></label>
            </div>
            <div class="toggle-item">
              <div>
                <h4>Atualizações de Status</h4>
                <p>Receber alerta no painel quando uma proposta mudar de status.</p>
              </div>
              <label class="switch"><input type="checkbox" checked><span class="slider"></span></label>
            </div>
            <div class="toggle-item">
              <div>
                <h4>Resumo Semanal</h4>
                <p>Receber relatório semanal de vendas e performance.</p>
              </div>
              <label class="switch"><input type="checkbox"><span class="slider"></span></label>
            </div>
            <div class="toggle-item">
              <div>
                <h4>Marketing e Novidades</h4>
                <p>Receber comunicados sobre novos recursos da plataforma.</p>
              </div>
              <label class="switch"><input type="checkbox"><span class="slider"></span></label>
            </div>
          </div>
        </div>

        <!-- Segurança -->
        <div v-if="activeTab === 'security'" class="tab-pane fade-in">
          <h2 class="pane-title">Segurança da Conta</h2>
          <p class="pane-desc">Mantenha sua conta protegida e segura.</p>
          <div class="form-grid">
            <div class="form-group full-width">
              <label>Senha Atual</label>
              <input type="password" class="form-input" placeholder="••••••••" />
            </div>
            <div class="form-group">
              <label>Nova Senha</label>
              <input type="password" class="form-input" placeholder="Nova senha segura" />
            </div>
            <div class="form-group">
              <label>Confirmar Nova Senha</label>
              <input type="password" class="form-input" placeholder="Confirme a nova senha" />
            </div>
            <div class="form-group full-width" style="margin-top: 8px;">
              <button class="btn-outline" style="width: max-content;">Atualizar Senha</button>
            </div>
          </div>
          
          <div class="security-card" style="margin-top: 40px;">
            <div style="display: flex; gap: 16px; align-items: flex-start;">
              <div style="background: #F0FDF4; color: #16A34A; padding: 12px; border-radius: 50%;">
                <ShieldCheck size="24" />
              </div>
              <div>
                <h4 style="font-size: 1.1rem; margin-bottom: 4px;">Autenticação em 2 Fatores (2FA)</h4>
                <p style="color: var(--text-muted); font-size: 0.9rem; margin-bottom: 16px; line-height: 1.5;">Adicione uma camada extra de segurança ativando o 2FA. Você precisará digitar um código enviado para seu celular sempre que fizer login.</p>
                <button class="btn-primary" style="background: var(--success); border-color: var(--success);">Ativar 2FA Agora</button>
              </div>
            </div>
          </div>
        </div>

        <!-- Personalização -->
        <div v-if="activeTab === 'appearance'" class="tab-pane fade-in">
          <h2 class="pane-title">Personalização e Branding</h2>
          <p class="pane-desc">Ajuste as cores e a aparência dos documentos gerados para os clientes.</p>
          
          <div class="form-group full-width" style="margin-bottom: 24px;">
            <label>Cor Principal da Marca (Hexadecimal)</label>
            <div style="display: flex; gap: 12px; align-items: center; margin-top: 8px;">
              <input type="color" v-model="primaryColor" style="width: 48px; height: 48px; padding: 0; border: none; border-radius: 8px; cursor: pointer;" />
              <input type="text" class="form-input" v-model="primaryColor" style="width: 120px;" />
            </div>
          </div>

          <div class="form-group full-width">
            <label style="margin-bottom: 8px; display: block;">Tema do Painel</label>
            <div style="display: flex; gap: 16px;">
              <div class="theme-option" :class="{ selected: theme === 'light' }" @click="theme = 'light'">
                <div class="theme-preview light"></div>
                <span>Claro</span>
              </div>
              <div class="theme-option" :class="{ selected: theme === 'dark' }" @click="theme = 'dark'">
                <div class="theme-preview dark"></div>
                <span>Escuro</span>
              </div>
              <div class="theme-option" :class="{ selected: theme === 'system' }" @click="theme = 'system'">
                <div class="theme-preview system"></div>
                <span>Sistema</span>
              </div>
            </div>
          </div>
        </div>

        <!-- Integrações -->
        <div v-if="activeTab === 'integrations'" class="tab-pane fade-in">
          <h2 class="pane-title">Integrações de Aplicativos</h2>
          <p class="pane-desc">Conecte ferramentas externas para turbinar suas vendas.</p>
          
          <div class="integrations-list">
            <div v-for="app in integrations" :key="app.id" class="integration-card">
              <div class="integration-info">
                <img :src="app.icon" :alt="app.name" class="integration-icon" />
                <div>
                  <h4>{{ app.name }}</h4>
                  <p>{{ app.desc }}</p>
                </div>
              </div>
              <label class="switch">
                <input type="checkbox" v-model="app.status">
                <span class="slider"></span>
              </label>
            </div>
          </div>
        </div>
      </main>
    </div>
  </div>
</template>

<style scoped>
.settings {
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

.settings-layout {
  display: grid;
  grid-template-columns: 280px 1fr;
  gap: 24px;
  align-items: start;
}

.settings-nav {
  padding: 16px;
}

.settings-tab {
  display: flex;
  align-items: center;
  gap: 12px;
  width: 100%;
  padding: 12px 16px;
  border: none;
  background: transparent;
  color: var(--text-muted);
  font-family: var(--font-family);
  font-size: 0.95rem;
  font-weight: 500;
  border-radius: var(--radius-md);
  cursor: pointer;
  text-align: left;
  transition: all 0.2s;
  margin-bottom: 4px;
}

.settings-tab:hover {
  background-color: var(--bg-color);
  color: var(--text-main);
}

.settings-tab.active {
  background-color: var(--primary-light);
  color: var(--primary);
  font-weight: 600;
}

.settings-content {
  padding: 40px;
  min-height: 600px;
}

.pane-title {
  font-size: 1.35rem;
  font-weight: 600;
  margin-bottom: 6px;
}

.pane-desc {
  color: var(--text-muted);
  font-size: 0.95rem;
  margin-bottom: 32px;
}

.profile-pic-section {
  display: flex;
  align-items: center;
  gap: 24px;
}

.pic-placeholder {
  width: 80px;
  height: 80px;
  background-color: var(--primary);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  border-radius: 50%;
  font-size: 1.5rem;
  font-weight: 700;
}

.text-xs { font-size: 0.8rem; }
.font-medium { font-weight: 500; color: var(--text-main); }

.form-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 24px;
}

.form-group {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.form-group.full-width {
  grid-column: 1 / -1;
}

.form-group label {
  font-size: 0.85rem;
  font-weight: 500;
  color: var(--text-main);
}

.form-input {
  padding: 12px 16px;
  border: 1px solid var(--border);
  border-radius: var(--radius-md);
  font-family: var(--font-family);
  font-size: 0.95rem;
  outline: none;
  transition: border-color 0.2s;
  background-color: transparent;
}

.form-input:focus {
  border-color: var(--primary);
  box-shadow: 0 0 0 3px var(--primary-light);
}

.form-input:disabled {
  background-color: var(--bg-color);
  cursor: not-allowed;
  color: var(--text-muted);
}

.btn-outline {
  padding: 8px 16px;
  border: 1px solid var(--border);
  border-radius: var(--radius-md);
  background: white;
  color: var(--text-main);
  font-weight: 500;
  font-family: var(--font-family);
  cursor: pointer;
  transition: all 0.2s;
}

.btn-outline:hover {
  background: var(--bg-color);
}

.data-table th, .data-table td {
  padding: 16px;
}

.security-card {
  padding: 24px;
  border: 1px solid var(--border);
  border-radius: var(--radius-lg);
  background-color: white;
}

/* Toggles */
.toggle-group { display: flex; flex-direction: column; gap: 24px; }
.toggle-item { display: flex; justify-content: space-between; align-items: center; padding-bottom: 16px; border-bottom: 1px solid var(--border); }
.toggle-item h4 { font-size: 1rem; margin-bottom: 4px; font-weight: 500; }
.toggle-item p { font-size: 0.85rem; color: var(--text-muted); }

.switch { position: relative; display: inline-block; width: 44px; height: 24px; flex-shrink: 0; }
.switch input { opacity: 0; width: 0; height: 0; }
.slider { position: absolute; cursor: pointer; top: 0; left: 0; right: 0; bottom: 0; background-color: #E5E7EB; transition: .3s; border-radius: 24px; }
.slider:before { position: absolute; content: ""; height: 18px; width: 18px; left: 3px; bottom: 3px; background-color: white; transition: .3s; border-radius: 50%; box-shadow: 0 2px 4px rgba(0,0,0,0.1); }
input:checked + .slider { background-color: var(--primary); }
input:checked + .slider:before { transform: translateX(20px); }

/* Theme Selection */
.theme-option { display: flex; flex-direction: column; gap: 12px; align-items: center; cursor: pointer; }
.theme-preview { width: 120px; height: 80px; border-radius: 12px; border: 2px solid transparent; transition: all 0.2s; }
.theme-option:hover .theme-preview { border-color: var(--border); }
.theme-option.selected .theme-preview { border-color: var(--primary); box-shadow: 0 0 0 2px var(--primary-light); }
.theme-option span { font-size: 0.9rem; font-weight: 500; color: var(--text-muted); }
.theme-option.selected span { color: var(--text-main); font-weight: 600; }

.theme-preview.light { background: #F8F9FB; border: 2px solid var(--border); }
.theme-preview.dark { background: #111827; }
.theme-preview.system { background: linear-gradient(to right, #F8F9FB 50%, #111827 50%); border: 2px solid var(--border); }

/* Integrations */
.integrations-list { display: flex; flex-direction: column; gap: 16px; }
.integration-card { display: flex; align-items: center; justify-content: space-between; padding: 20px; border: 1px solid var(--border); border-radius: var(--radius-md); }
.integration-info { display: flex; align-items: center; gap: 16px; }
.integration-icon { width: 40px; height: 40px; object-fit: contain; }
.integration-info h4 { font-size: 1.05rem; font-weight: 600; margin-bottom: 4px; }
.integration-info p { font-size: 0.85rem; color: var(--text-muted); }

.fade-in {
  animation: fadeIn 0.3s ease-out forwards;
}

@keyframes fadeIn {
  from { opacity: 0; transform: translateY(5px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>
