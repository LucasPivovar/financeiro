<script setup>
import { ref, onMounted, onUnmounted, nextTick } from 'vue';
import { MoreVertical, Edit2, Trash2 } from '@lucide/vue';

const props = defineProps({
  item: {
    type: Object,
    required: true
  }
});

const emit = defineEmits(['edit', 'delete']);

const isOpen = ref(false);
const dropdownRef = ref(null);

const toggleDropdown = () => {
  isOpen.value = !isOpen.value;
};

const closeDropdown = (e) => {
  if (dropdownRef.value && !dropdownRef.value.contains(e.target)) {
    isOpen.value = false;
  }
};

onMounted(() => {
  document.addEventListener('click', closeDropdown);
});

onUnmounted(() => {
  document.removeEventListener('click', closeDropdown);
});

const handleEdit = () => {
  isOpen.value = false;
  emit('edit', props.item);
};

const handleDelete = () => {
  isOpen.value = false;
  emit('delete', props.item);
};
</script>

<template>
  <div class="dropdown-wrapper" ref="dropdownRef">
    <button class="icon-btn" @click.stop="toggleDropdown">
      <MoreVertical size="18" />
    </button>
    
    <Transition name="fade">
      <div v-if="isOpen" class="dropdown-menu">
        <button class="dropdown-item" @click="handleEdit">
          <Edit2 size="16" />
          Editar
        </button>
        <button class="dropdown-item danger" @click="handleDelete">
          <Trash2 size="16" />
          Excluir
        </button>
      </div>
    </Transition>
  </div>
</template>

<style scoped>
.dropdown-wrapper {
  position: relative;
  display: inline-block;
}

.dropdown-menu {
  position: absolute;
  right: 0;
  top: 100%;
  margin-top: 4px;
  background-color: white;
  border-radius: var(--radius-md);
  box-shadow: var(--shadow-card);
  border: 1px solid var(--border);
  min-width: 150px;
  z-index: 50;
  display: flex;
  flex-direction: column;
  padding: 4px;
}

.dropdown-item {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 12px;
  border: none;
  background: transparent;
  width: 100%;
  text-align: left;
  font-size: 0.85rem;
  font-weight: 500;
  font-family: var(--font-family);
  color: var(--text-main);
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.dropdown-item:hover {
  background-color: var(--bg-color);
}

.dropdown-item.danger {
  color: var(--danger);
}

.dropdown-item.danger:hover {
  background-color: var(--danger-bg);
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s, transform 0.2s;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: translateY(-5px);
}
</style>
