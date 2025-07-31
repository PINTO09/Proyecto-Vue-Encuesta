<template>
  <div class="container-fluid">
    <div class="row">
      <div class="col-12">
        <div class="d-flex justify-content-between align-items-center mb-4">
          <h2><i class="fas fa-chart-bar me-2"></i>Resultados de Encuestas</h2>
          <button class="btn btn-secondary" @click="$emit('navigate', 'admin-dashboard')">
            <i class="fas fa-arrow-left me-2"></i>Volver
          </button>
        </div>

        <!-- Filtros -->
        <div class="card mb-4">
          <div class="card-body">
            <div class="row">
              <div class="col-md-3">
                <label class="form-label">Encuesta</label>
                <select v-model="selectedSurvey" class="form-select" @change="loadResults">
                  <option value="">Seleccionar encuesta</option>
                  <option v-for="survey in surveys" :key="survey.id" :value="survey.id">
                    {{ survey.title }}
                  </option>
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
              <div class="col-md-3">
                <label class="form-label">Rango de Fechas</label>
                <select v-model="filters.dateRange" class="form-select">
                  <option value="">Todos</option>
                  <option value="today">Hoy</option>
                  <option value="week">Esta semana</option>
                  <option value="month">Este mes</option>
                </select>
              </div>
              <div class="col-md-3">
                <label class="form-label">&nbsp;</label>
                <button class="btn btn-primary w-100" @click="exportResults">
                  <i class="fas fa-download me-2"></i>Exportar
                </button>
              </div>
            </div>
          </div>
        </div>

        <!-- Resumen de resultados -->
        <div v-if="selectedSurvey" class="row mb-4">
          <div class="col-md-3">
            <div class="card bg-primary text-white">
              <div class="card-body">
                <div class="d-flex justify-content-between">
                  <div>
                    <h6 class="card-title">Total Respuestas</h6>
                    <h3>{{ surveyResults.totalResponses }}</h3>
                  </div>
                  <div class="align-self-center">
                    <i class="fas fa-users fa-2x"></i>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="col-md-3">
            <div class="card bg-success text-white">
              <div class="card-body">
                <div class="d-flex justify-content-between">
                  <div>
                    <h6 class="card-title">Tasa de Respuesta</h6>
                    <h3>{{ surveyResults.responseRate }}%</h3>
                  </div>
                  <div class="align-self-center">
                    <i class="fas fa-percentage fa-2x"></i>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="col-md-3">
            <div class="card bg-info text-white">
              <div class="card-body">
                <div class="d-flex justify-content-between">
                  <div>
                    <h6 class="card-title">Promedio General</h6>
                    <h3>{{ surveyResults.averageScore }}/5</h3>
                  </div>
                  <div class="align-self-center">
                    <i class="fas fa-star fa-2x"></i>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="col-md-3">
            <div class="card bg-warning text-dark">
              <div class="card-body">
                <div class="d-flex justify-content-between">
                  <div>
                    <h6 class="card-title">Última Respuesta</h6>
                    <h6>{{ surveyResults.lastResponse }}</h6>
                  </div>
                  <div class="align-self-center">
                    <i class="fas fa-clock fa-2x"></i>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>

        <!-- Gráficos y análisis -->
        <div v-if="selectedSurvey" class="row">
          <div class="col-md-6">
            <div class="card">
              <div class="card-header">
                <h5><i class="fas fa-chart-pie me-2"></i>Distribución por Facultad</h5>
              </div>
              <div class="card-body">
                <canvas ref="facultyChart" width="400" height="200"></canvas>
              </div>
            </div>
          </div>
          <div class="col-md-6">
            <div class="card">
              <div class="card-header">
                <h5><i class="fas fa-chart-line me-2"></i>Tendencia de Respuestas</h5>
              </div>
              <div class="card-body">
                <canvas ref="trendChart" width="400" height="200"></canvas>
              </div>
            </div>
          </div>
        </div>

        <!-- Tabla de respuestas detalladas -->
        <div v-if="selectedSurvey" class="card mt-4">
          <div class="card-header">
            <h5><i class="fas fa-table me-2"></i>Respuestas Detalladas</h5>
          </div>
          <div class="card-body">
            <div class="table-responsive">
              <table class="table table-hover">
                <thead class="table-dark">
                  <tr>
                    <th>ID</th>
                    <th>Estudiante</th>
                    <th>Facultad</th>
                    <th>Puntuación</th>
                    <th>Fecha</th>
                    <th>Acciones</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="response in filteredResponses" :key="response.id">
                    <td>{{ response.id }}</td>
                    <td>{{ response.studentName }}</td>
                    <td>{{ response.faculty }}</td>
                    <td>
                      <div class="d-flex align-items-center">
                        <span class="me-2">{{ response.score }}/5</span>
                        <div class="progress flex-grow-1" style="height: 8px;">
                          <div 
                            class="progress-bar" 
                            :style="{ width: (response.score / 5) * 100 + '%' }"
                          ></div>
                        </div>
                      </div>
                    </td>
                    <td>{{ formatDate(response.date) }}</td>
                    <td>
                      <button 
                        class="btn btn-sm btn-outline-info" 
                        @click="viewResponseDetails(response)"
                        title="Ver Detalles"
                      >
                        <i class="fas fa-eye"></i>
                      </button>
                    </td>
                  </tr>
                </tbody>
              </table>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Modal para detalles de respuesta -->
    <div class="modal fade" :class="{ show: showResponseModal }" :style="{ display: showResponseModal ? 'block' : 'none' }" tabindex="-1">
      <div class="modal-dialog modal-lg">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Detalles de Respuesta</h5>
            <button type="button" class="btn-close" @click="closeResponseModal"></button>
          </div>
          <div class="modal-body">
            <div v-if="selectedResponse">
              <div class="row mb-3">
                <div class="col-md-6">
                  <strong>Estudiante:</strong> {{ selectedResponse.studentName }}
                </div>
                <div class="col-md-6">
                  <strong>Fecha:</strong> {{ formatDate(selectedResponse.date) }}
                </div>
              </div>
              <div class="row mb-3">
                <div class="col-md-6">
                  <strong>Facultad:</strong> {{ selectedResponse.faculty }}
                </div>
                <div class="col-md-6">
                  <strong>Puntuación:</strong> {{ selectedResponse.score }}/5
                </div>
              </div>
              
              <h6>Respuestas a Preguntas:</h6>
              <div v-for="(answer, index) in selectedResponse.answers" :key="index" class="mb-3">
                <div class="card">
                  <div class="card-body">
                    <h6 class="card-title">{{ answer.question }}</h6>
                    <p class="card-text">{{ answer.answer }}</p>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" @click="closeResponseModal">Cerrar</button>
          </div>
        </div>
      </div>
    </div>
    
    <!-- Overlay para modal -->
    <div 
      v-if="showResponseModal" 
      class="modal-backdrop fade show"
      @click="closeResponseModal"
    ></div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, nextTick } from 'vue'

// Props y emits
const emit = defineEmits(['navigate'])

// Estado reactivo
const surveys = ref([
  {
    id: 1,
    title: 'Satisfacción Estudiantil 2024',
    faculty: 'Facultad de Ingeniería'
  },
  {
    id: 2,
    title: 'Evaluación Docente - Semestre 2024-1',
    faculty: 'Facultad de Medicina'
  },
  {
    id: 3,
    title: 'Infraestructura Universitaria',
    faculty: 'Facultad de Educación'
  }
])

const selectedSurvey = ref('')
const filters = ref({
  faculty: '',
  dateRange: ''
})

const surveyResults = ref({
  totalResponses: 0,
  responseRate: 0,
  averageScore: 0,
  lastResponse: ''
})

const responses = ref([])
const showResponseModal = ref(false)
const selectedResponse = ref(null)

// Referencias para gráficos
const facultyChart = ref(null)
const trendChart = ref(null)

// Computed properties
const filteredResponses = computed(() => {
  return responses.value.filter(response => {
    const matchesFaculty = !filters.value.faculty || response.faculty.toLowerCase().includes(filters.value.faculty.toLowerCase())
    return matchesFaculty
  })
})

// Métodos
const loadResults = () => {
  if (!selectedSurvey.value) return
  
  // Simular carga de datos
  surveyResults.value = {
    totalResponses: 45,
    responseRate: 75,
    averageScore: 4.2,
    lastResponse: 'Hace 2 horas'
  }
  
  responses.value = [
    {
      id: 1,
      studentName: 'Juan Pérez',
      faculty: 'Facultad de Ingeniería',
      score: 4,
      date: '2024-01-15T10:30:00',
      answers: [
        { question: '¿Cómo calificaría la calidad de la enseñanza?', answer: 'Muy buena' },
        { question: '¿Recomendaría esta universidad?', answer: 'Sí' }
      ]
    },
    {
      id: 2,
      studentName: 'María García',
      faculty: 'Facultad de Medicina',
      score: 5,
      date: '2024-01-15T09:15:00',
      answers: [
        { question: '¿Cómo calificaría la calidad de la enseñanza?', answer: 'Excelente' },
        { question: '¿Recomendaría esta universidad?', answer: 'Definitivamente sí' }
      ]
    }
  ]
  
  nextTick(() => {
    createCharts()
  })
}

const createCharts = () => {
  // Crear gráfico de distribución por facultad
  if (facultyChart.value) {
    const ctx = facultyChart.value.getContext('2d')
    // Aquí se implementaría Chart.js para crear el gráfico
    // Por ahora solo dibujamos un rectángulo simple
    ctx.fillStyle = '#007bff'
    ctx.fillRect(0, 0, 200, 100)
  }
  
  // Crear gráfico de tendencia
  if (trendChart.value) {
    const ctx = trendChart.value.getContext('2d')
    ctx.fillStyle = '#28a745'
    ctx.fillRect(0, 0, 200, 100)
  }
}

const formatDate = (dateString) => {
  return new Date(dateString).toLocaleDateString('es-ES')
}

const exportResults = () => {
  // Implementar exportación de resultados
  alert('Función de exportación en desarrollo')
}

const viewResponseDetails = (response) => {
  selectedResponse.value = response
  showResponseModal.value = true
}

const closeResponseModal = () => {
  showResponseModal.value = false
  selectedResponse.value = null
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

.progress {
  background-color: #e9ecef;
}

.progress-bar {
  background-color: #007bff;
}
</style> 