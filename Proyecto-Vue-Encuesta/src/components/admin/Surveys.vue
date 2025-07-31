<template>
  <div class="container-fluid">
    <div class="row">
      <div class="col-12">
        <div class="d-flex justify-content-between align-items-center mb-4">
          <h2><i class="fas fa-clipboard-list me-2"></i>Gestión de Encuestas</h2>
          <button class="btn btn-primary" @click="showCreateModal = true">
            <i class="fas fa-plus me-2"></i>Nueva Encuesta
          </button>
        </div>

        <!-- Filtros -->
        <div class="card mb-4">
          <div class="card-body">
            <div class="row">
              <div class="col-md-3">
                <label class="form-label">Estado</label>
                <select v-model="filters.status" class="form-select">
                  <option value="">Todos</option>
                  <option value="activa">Activa</option>
                  <option value="inactiva">Inactiva</option>
                  <option value="borrador">Borrador</option>
                </select>
              </div>
              <div class="col-md-3">
                <label class="form-label">Facultad</label>
                <select v-model="filters.faculty" class="form-select">
                  <option value="">Todas</option>
                  <option value="ingenieria">Ingeniería</option>
                  <option value="medicina">Medicina</option>
                  <option value="educacion">Educación</option>
                  <option value="administracion">Administración</option>
                </select>
              </div>
              <div class="col-md-4">
                <label class="form-label">Buscar</label>
                <input 
                  v-model="filters.search" 
                  type="text" 
                  class="form-control" 
                  placeholder="Buscar por título o descripción..."
                >
              </div>
              <div class="col-md-2">
                <label class="form-label">&nbsp;</label>
                <button class="btn btn-secondary w-100" @click="clearFilters">
                  <i class="fas fa-times me-2"></i>Limpiar
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- Tabla de encuestas -->
        <div class="card">
          <div class="card-body">
            <div class="table-responsive">
              <table class="table table-hover">
                <thead class="table-dark">
                  <tr>
                    <th>Título</th>
                    <th>Descripción</th>
                    <th>Facultad</th>
                    <th>Estado</th>
                    <th>Respuestas</th>
                    <th>Fecha Creación</th>
                    <th>Acciones</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="survey in filteredSurveys" :key="survey.id">
                    <td>{{ survey.title }}</td>
                    <td>{{ survey.description }}</td>
                    <td>{{ survey.faculty }}</td>
                    <td>
                      <span :class="getStatusBadgeClass(survey.status)">
                        {{ survey.status }}
                      </span>
                    </td>
                    <td>{{ survey.responses }}/{{ survey.target }}</td>
                    <td>{{ formatDate(survey.createdAt) }}</td>
                    <td>
                      <div class="btn-group" role="group">
                        <button 
                          class="btn btn-sm btn-outline-primary" 
                          @click="editSurvey(survey)"
                          title="Editar"
                        >
                          <i class="fas fa-edit"></i>
                        </button>
                        <button 
                          class="btn btn-sm btn-outline-info" 
                          @click="viewResults(survey)"
                          title="Ver Resultados"
                        >
                          <i class="fas fa-chart-bar"></i>
                        </button>
                        <button 
                          class="btn btn-sm btn-outline-success" 
                          @click="duplicateSurvey(survey)"
                          title="Duplicar"
                        >
                          <i class="fas fa-copy"></i>
                        </button>
                        <button 
                          class="btn btn-sm btn-outline-danger" 
                          @click="deleteSurvey(survey)"
                          title="Eliminar"
                        >
                          <i class="fas fa-trash"></i>
                        </button>
                      </div>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal para crear/editar encuesta -->
    <div class="modal fade" :class="{ show: showCreateModal }" :style="{ display: showCreateModal ? 'block' : 'none' }" tabindex="-1">
      <div class="modal-dialog modal-lg">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">
              {{ editingSurvey ? 'Editar Encuesta' : 'Nueva Encuesta' }}
            </h5>
            <button type="button" class="btn-close" @click="closeModal"></button>
          </div>
          <div class="modal-body">
            <form @submit.prevent="saveSurvey">
              <div class="row">
                <div class="col-md-8">
                  <div class="mb-3">
                    <label class="form-label">Título de la Encuesta</label>
                    <input 
                      v-model="surveyForm.title" 
                      type="text" 
                      class="form-control" 
                      required
                    >
                  </div>
                </div>
                <div class="col-md-4">
                  <div class="mb-3">
                    <label class="form-label">Estado</label>
                    <select v-model="surveyForm.status" class="form-select" required>
                      <option value="borrador">Borrador</option>
                      <option value="activa">Activa</option>
                      <option value="inactiva">Inactiva</option>
                    </select>
                  </div>
                </div>
              </div>
              
              <div class="mb-3">
                <label class="form-label">Descripción</label>
                <textarea 
                  v-model="surveyForm.description" 
                  class="form-control" 
                  rows="3"
                  required
                ></textarea>
              </div>
              
              <div class="row">
                <div class="col-md-6">
                  <div class="mb-3">
                    <label class="form-label">Facultad</label>
                    <select v-model="surveyForm.faculty" class="form-select" required>
                      <option value="">Seleccionar facultad</option>
                      <option value="ingenieria">Facultad de Ingeniería</option>
                      <option value="medicina">Facultad de Medicina</option>
                      <option value="educacion">Facultad de Educación</option>
                      <option value="administracion">Facultad de Administración</option>
                    </select>
                  </div>
                </div>
                <div class="col-md-6">
                  <div class="mb-3">
                    <label class="form-label">Meta de Respuestas</label>
                    <input 
                      v-model="surveyForm.target" 
                      type="number" 
                      class="form-control" 
                      min="1"
                      required
                    >
                  </div>
                </div>
              </div>
              
              <div class="row">
                <div class="col-md-6">
                  <div class="mb-3">
                    <label class="form-label">Fecha de Inicio</label>
                    <input 
                      v-model="surveyForm.startDate" 
                      type="date" 
                      class="form-control"
                      required
                    >
                  </div>
                </div>
                <div class="col-md-6">
                  <div class="mb-3">
                    <label class="form-label">Fecha de Fin</label>
                    <input 
                      v-model="surveyForm.endDate" 
                      type="date" 
                      class="form-control"
                      required
                    >
                  </div>
                </div>
              </div>
            </form>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" @click="closeModal">Cancelar</button>
            <button type="button" class="btn btn-primary" @click="saveSurvey">
              {{ editingSurvey ? 'Actualizar' : 'Crear' }}
            </button>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Overlay para modal -->
    <div 
      v-if="showCreateModal" 
      class="modal-backdrop fade show"
      @click="closeModal"
    ></div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

// Props y emits
const emit = defineEmits(['navigate'])

// Estado reactivo
const surveys = ref([
  {
    id: 1,
    title: 'Satisfacción Estudiantil 2024',
    description: 'Encuesta para evaluar la satisfacción de los estudiantes con los servicios universitarios',
    faculty: 'Facultad de Ingeniería',
    status: 'activa',
    responses: 45,
    target: 100,
    createdAt: '2024-01-15'
  },
  {
    id: 2,
    title: 'Evaluación Docente - Semestre 2024-1',
    description: 'Evaluación de la calidad docente y metodología de enseñanza',
    faculty: 'Facultad de Medicina',
    status: 'activa',
    responses: 78,
    target: 120,
    createdAt: '2024-01-10'
  },
  {
    id: 3,
    title: 'Infraestructura Universitaria',
    description: 'Evaluación de las instalaciones y servicios de la universidad',
    faculty: 'Facultad de Educación',
    status: 'borrador',
    responses: 0,
    target: 80,
    createdAt: '2024-01-20'
  }
])

const filters = ref({
  status: '',
  faculty: '',
  search: ''
})

const showCreateModal = ref(false)
const editingSurvey = ref(null)
const surveyForm = ref({
  title: '',
  description: '',
  faculty: '',
  status: 'borrador',
  target: 50,
  startDate: '',
  endDate: ''
})

// Computed properties
const filteredSurveys = computed(() => {
  return surveys.value.filter(survey => {
    const matchesStatus = !filters.value.status || survey.status === filters.value.status
    const matchesFaculty = !filters.value.faculty || survey.faculty.toLowerCase().includes(filters.value.faculty.toLowerCase())
    const matchesSearch = !filters.value.search || 
      survey.title.toLowerCase().includes(filters.value.search.toLowerCase()) ||
      survey.description.toLowerCase().includes(filters.value.search.toLowerCase())
    
    return matchesStatus && matchesFaculty && matchesSearch
  })
})

// Métodos
const getStatusBadgeClass = (status) => {
  const classes = {
    activa: 'badge bg-success',
    inactiva: 'badge bg-secondary',
    borrador: 'badge bg-warning text-dark'
  }
  return classes[status] || 'badge bg-secondary'
}

const formatDate = (dateString) => {
  return new Date(dateString).toLocaleDateString('es-ES')
}

const clearFilters = () => {
  filters.value = {
    status: '',
    faculty: '',
    search: ''
  }
}

const editSurvey = (survey) => {
  editingSurvey.value = survey
  surveyForm.value = { ...survey }
  showCreateModal.value = true
}

const viewResults = (survey) => {
  emit('navigate', 'admin-results', { surveyId: survey.id })
}

const duplicateSurvey = (survey) => {
  const newSurvey = {
    ...survey,
    id: Date.now(),
    title: `${survey.title} (Copia)`,
    status: 'borrador',
    responses: 0,
    createdAt: new Date().toISOString().split('T')[0]
  }
  surveys.value.push(newSurvey)
}

const deleteSurvey = (survey) => {
  if (confirm('¿Está seguro de que desea eliminar esta encuesta?')) {
    const index = surveys.value.findIndex(s => s.id === survey.id)
    if (index > -1) {
      surveys.value.splice(index, 1)
    }
  }
}

const closeModal = () => {
  showCreateModal.value = false
  editingSurvey.value = null
  surveyForm.value = {
    title: '',
    description: '',
    faculty: '',
    status: 'borrador',
    target: 50,
    startDate: '',
    endDate: ''
  }
}

const saveSurvey = () => {
  if (editingSurvey.value) {
    // Actualizar encuesta existente
    const index = surveys.value.findIndex(s => s.id === editingSurvey.value.id)
    if (index > -1) {
      surveys.value[index] = { ...editingSurvey.value, ...surveyForm.value }
    }
  } else {
    // Crear nueva encuesta
    const newSurvey = {
      id: Date.now(),
      ...surveyForm.value,
      responses: 0,
      createdAt: new Date().toISOString().split('T')[0]
    }
    surveys.value.push(newSurvey)
  }
  
  closeModal()
}

// Lifecycle
onMounted(() => {
  // Cargar encuestas desde localStorage si existen
  const storedSurveys = localStorage.getItem('adminSurveys')
  if (storedSurveys) {
    surveys.value = JSON.parse(storedSurveys)
  }
})
</script>

<style scoped>
.modal.show {
  background-color: rgba(0, 0, 0, 0.5);
}

.btn-group .btn {
  margin-right: 2px;
}

.btn-group .btn:last-child {
  margin-right: 0;
}
</style> 