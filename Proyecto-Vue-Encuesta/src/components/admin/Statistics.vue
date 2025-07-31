<script setup>
import { ref } from 'vue'

const props = defineProps({
  user: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['navigate'])

const navigateTo = (view) => {
  emit('navigate', view)
}
</script>

<template>
  <div class="admin-statistics">
    <div class="d-flex justify-content-between align-items-center mb-4">
      <h2>Estadísticas</h2>
      <button class="btn btn-primary" @click="navigateTo('admin-dashboard')">
        <i class="fas fa-arrow-left me-2"></i>
        Volver al Dashboard
      </button>
    </div>

    <div class="card">
      <div class="card-body">
        <h5 class="card-title">Estadísticas del Sistema</h5>
        <p class="text-muted">Esta sección mostrará estadísticas detalladas del sistema de encuestas.</p>
        <div class="text-center py-5">
          <i class="fas fa-chart-line fa-3x text-muted mb-3"></i>
          <p>Funcionalidad en desarrollo</p>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.admin-statistics {
  padding: 2rem;
}

.card {
  border: none;
  border-radius: 15px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.btn {
  border-radius: 8px;
  font-weight: 500;
  transition: all 0.3s ease;
}

.btn:hover {
  transform: translateY(-1px);
}

@media (max-width: 768px) {
  .admin-statistics {
    padding: 1rem;
  }
}
</style> 