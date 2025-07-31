<script setup>
import { ref, onMounted } from 'vue'

const props = defineProps({
  user: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['navigate'])

const users = ref([])
const loading = ref(true)

onMounted(() => {
  // Cargar usuarios desde localStorage
  const storedUsers = JSON.parse(localStorage.getItem('usuarios') || '[]')
  const defaultUsers = [
    {
      username: 'admin',
      role: 'admin',
      name: 'Administrador',
      email: 'admin@uleam.edu.ec',
      facultad: 'Administración',
      carrera: 'Administración',
      semestre: 'N/A',
      fechaRegistro: '2024-01-01'
    }
  ]
  users.value = [...defaultUsers, ...storedUsers]
  loading.value = false
})

const navigateTo = (view) => {
  emit('navigate', view)
}

const deleteUser = (userId) => {
  if (confirm('¿Está seguro de que desea eliminar este usuario?')) {
    users.value = users.value.filter(user => user.username !== userId)
    // Actualizar localStorage
    const nonAdminUsers = users.value.filter(user => user.role !== 'admin')
    localStorage.setItem('usuarios', JSON.stringify(nonAdminUsers))
  }
}
</script>

<template>
  <div class="admin-users">
    <div class="d-flex justify-content-between align-items-center mb-4">
      <h2>Gestión de Usuarios</h2>
      <button class="btn btn-primary" @click="navigateTo('admin-dashboard')">
        <i class="fas fa-arrow-left me-2"></i>
        Volver al Dashboard
      </button>
    </div>

    <div class="card">
      <div class="card-header">
        <h5 class="card-title mb-0">Lista de Usuarios</h5>
      </div>
      <div class="card-body">
        <div v-if="loading" class="text-center py-4">
          <div class="spinner-border text-primary" role="status">
            <span class="visually-hidden">Cargando...</span>
          </div>
        </div>
        
        <div v-else class="table-responsive">
          <table class="table table-hover">
            <thead>
              <tr>
                <th>Usuario</th>
                <th>Nombre</th>
                <th>Email</th>
                <th>Rol</th>
                <th>Facultad</th>
                <th>Carrera</th>
                <th>Semestre</th>
                <th>Fecha Registro</th>
                <th>Acciones</th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="user in users" :key="user.username">
                <td>{{ user.username }}</td>
                <td>{{ user.name || `${user.nombre} ${user.apellido}` }}</td>
                <td>{{ user.email || 'N/A' }}</td>
                <td>
                  <span :class="user.role === 'admin' ? 'badge bg-danger' : 'badge bg-primary'">
                    {{ user.role === 'admin' ? 'Administrador' : 'Estudiante' }}
                  </span>
                </td>
                <td>{{ user.facultad || 'N/A' }}</td>
                <td>{{ user.carrera || 'N/A' }}</td>
                <td>{{ user.semestre || 'N/A' }}</td>
                <td>{{ new Date(user.fechaRegistro).toLocaleDateString() }}</td>
                <td>
                  <button 
                    class="btn btn-sm btn-outline-danger"
                    @click="deleteUser(user.username)"
                    :disabled="user.role === 'admin'"
                  >
                    <i class="fas fa-trash"></i>
                  </button>
                </td>
              </tr>
            </tbody>
          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.admin-users {
  padding: 2rem;
}

.card {
  border: none;
  border-radius: 15px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.card-header {
  background-color: #f8f9fa;
  border-bottom: 1px solid #dee2e6;
  border-radius: 15px 15px 0 0 !important;
}

.table th {
  border-top: none;
  font-weight: 600;
  color: #495057;
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
  .admin-users {
    padding: 1rem;
  }
  
  .table-responsive {
    font-size: 0.9rem;
  }
}
</style> 