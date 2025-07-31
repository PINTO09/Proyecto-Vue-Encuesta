<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  user: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['navigate'])

// Datos de participación del estudiante
const participationStats = ref({
  totalEncuestas: 15,
  completadas: 12,
  pendientes: 3
})

// Encuestas disponibles
const availableSurveys = ref([
  {
    id: 1,
    titulo: 'Satisfacción Estudiantil 2024',
    fechaLimite: '2024-02-15',
    estado: 'Disponible',
    descripcion: 'Encuesta para evaluar la satisfacción general de los estudiantes'
  },
  {
    id: 2,
    titulo: 'Evaluación de Infraestructura',
    fechaLimite: '2024-02-20',
    estado: 'Disponible',
    descripcion: 'Evaluación de las instalaciones y servicios universitarios'
  },
  {
    id: 3,
    titulo: 'Calidad de la Enseñanza',
    fechaLimite: '2024-02-25',
    estado: 'Disponible',
    descripcion: 'Evaluación de la calidad de la enseñanza y metodologías'
  }
])

// Encuestas recientes completadas
const recentSurveys = ref([
  {
    id: 4,
    titulo: 'Servicios Estudiantiles',
    fechaCompletado: '2024-01-20',
    estado: 'Completada',
    puntuacion: 4.5
  },
  {
    id: 5,
    titulo: 'Evaluación Docente',
    fechaCompletado: '2024-01-18',
    estado: 'Completada',
    puntuacion: 4.2
  },
  {
    id: 6,
    titulo: 'Biblioteca Universitaria',
    fechaCompletado: '2024-01-15',
    estado: 'Completada',
    puntuacion: 4.8
  }
])

// Computed properties para la información del estudiante
const studentInfo = computed(() => ({
  nombre: props.user.name || `${props.user.nombre} ${props.user.apellido}`,
  facultad: props.user.facultad ? getFacultadName(props.user.facultad) : 'No especificada',
  carrera: props.user.carrera ? getCarreraName(props.user.carrera) : 'No especificada',
  semestre: props.user.semestre ? `${props.user.semestre}° Semestre` : 'No especificado'
}))

// Función para obtener el nombre de la facultad
const getFacultadName = (facultad) => {
  const facultades = {
    ciencias: 'Facultad de Ciencias',
    ingenieria: 'Facultad de Ingeniería',
    medicina: 'Facultad de Medicina',
    sociales: 'Facultad de Ciencias Sociales',
    educacion: 'Facultad de Educación'
  }
  return facultades[facultad] || facultad
}

// Función para obtener el nombre de la carrera
const getCarreraName = (carrera) => {
  const carreras = {
    matematicas: 'Matemáticas',
    fisica: 'Física',
    quimica: 'Química',
    biologia: 'Biología',
    ingenieria_en_sistemas: 'Ingeniería en Sistemas',
    ingenieria_civil: 'Ingeniería Civil',
    ingenieria_industrial: 'Ingeniería Industrial',
    ingenieria_electrica: 'Ingeniería Eléctrica',
    ingenieria_mecanica: 'Ingeniería Mecánica',
    medicina: 'Medicina',
    enfermeria: 'Enfermería',
    odontologia: 'Odontología',
    farmacia: 'Farmacia',
    psicologia: 'Psicología',
    sociologia: 'Sociología',
    trabajo_social: 'Trabajo Social',
    comunicacion_social: 'Comunicación Social',
    educacion_basica: 'Educación Básica',
    educacion_inicial: 'Educación Inicial',
    educacion_especial: 'Educación Especial',
    educacion_fisica: 'Educación Física'
  }
  return carreras[carrera] || carrera
}

const navigateTo = (view) => {
  emit('navigate', view)
}

const startSurvey = (surveyId) => {
  // Aquí se podría navegar a la encuesta específica
  console.log('Iniciando encuesta:', surveyId)
  navigateTo('student-respond')
}

const viewSurvey = (surveyId) => {
  // Aquí se podría ver los resultados de la encuesta
  console.log('Viendo encuesta:', surveyId)
}
</script>

<template>
  <div class="student-dashboard">
    <!-- Información del Estudiante -->
    <div class="row mb-4">
      <div class="col-md-4">
        <div class="card">
          <div class="card-body">
            <h5 class="card-title">Información Personal</h5>
            <div class="student-info">
              <p><strong>Nombre:</strong> {{ studentInfo.nombre }}</p>
              <p><strong>Facultad:</strong> {{ studentInfo.facultad }}</p>
              <p><strong>Carrera:</strong> {{ studentInfo.carrera }}</p>
              <p><strong>Semestre:</strong> {{ studentInfo.semestre }}</p>
            </div>
          </div>
        </div>
      </div>
      <div class="col-md-8">
        <div class="card">
          <div class="card-body">
            <h5 class="card-title">Resumen de Participación</h5>
            <div class="row">
              <div class="col-md-4">
                <div class="text-center">
                  <h3 class="text-primary">{{ participationStats.totalEncuestas }}</h3>
                  <p class="text-muted">Total Encuestas</p>
                </div>
              </div>
              <div class="col-md-4">
                <div class="text-center">
                  <h3 class="text-success">{{ participationStats.completadas }}</h3>
                  <p class="text-muted">Completadas</p>
                </div>
              </div>
              <div class="col-md-4">
                <div class="text-center">
                  <h3 class="text-warning">{{ participationStats.pendientes }}</h3>
                  <p class="text-muted">Pendientes</p>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- Encuestas Disponibles -->
    <div class="row">
      <div class="col-12">
        <div class="card">
          <div class="card-header d-flex justify-content-between align-items-center">
            <h5 class="card-title mb-0">Encuestas Disponibles</h5>
            <button 
              class="btn btn-primary btn-sm"
              @click="navigateTo('student-surveys')"
            >
              Ver todas
            </button>
          </div>
          <div class="card-body">
            <div class="table-responsive">
              <table class="table table-custom">
                <thead>
                  <tr>
                    <th>Encuesta</th>
                    <th>Descripción</th>
                    <th>Fecha Límite</th>
                    <th>Estado</th>
                    <th>Acciones</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="encuesta in availableSurveys" :key="encuesta.id">
                    <td>{{ encuesta.titulo }}</td>
                    <td>{{ encuesta.descripcion }}</td>
                    <td>{{ new Date(encuesta.fechaLimite).toLocaleDateString() }}</td>
                    <td>
                      <span class="badge bg-success">{{ encuesta.estado }}</span>
                    </td>
                    <td>
                      <button 
                        class="btn btn-sm btn-primary"
                        @click="startSurvey(encuesta.id)"
                      >
                        <i class="fas fa-play me-1"></i>
                        Comenzar
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

    <!-- Encuestas Recientes -->
    <div class="row mt-4">
      <div class="col-12">
        <div class="card">
          <div class="card-header">
            <h5 class="card-title mb-0">Encuestas Recientes</h5>
          </div>
          <div class="card-body">
            <div class="table-responsive">
              <table class="table table-custom">
                <thead>
                  <tr>
                    <th>Encuesta</th>
                    <th>Fecha de Completado</th>
                    <th>Estado</th>
                    <th>Puntuación</th>
                    <th>Acciones</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="encuesta in recentSurveys" :key="encuesta.id">
                    <td>{{ encuesta.titulo }}</td>
                    <td>{{ new Date(encuesta.fechaCompletado).toLocaleDateString() }}</td>
                    <td>
                      <span class="badge bg-success">{{ encuesta.estado }}</span>
                    </td>
                    <td>
                      <div class="d-flex align-items-center">
                        <span class="me-2">{{ encuesta.puntuacion }}</span>
                        <div class="stars">
                          <i 
                            v-for="star in 5" 
                            :key="star"
                            :class="star <= Math.round(encuesta.puntuacion) ? 'fas fa-star text-warning' : 'far fa-star text-muted'"
                          ></i>
                        </div>
                      </div>
                    </td>
                    <td>
                      <button 
                        class="btn btn-sm btn-outline-info"
                        @click="viewSurvey(encuesta.id)"
                      >
                        <i class="fas fa-eye me-1"></i>
                        Ver
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

    <!-- Acciones Rápidas -->
    <div class="row mt-4">
      <div class="col-12">
        <div class="card">
          <div class="card-header">
            <h5 class="card-title mb-0">Acciones Rápidas</h5>
          </div>
          <div class="card-body">
            <div class="row">
              <div class="col-md-4 mb-3">
                <button 
                  class="btn btn-primary w-100"
                  @click="navigateTo('student-surveys')"
                >
                  <i class="fas fa-list me-2"></i>
                  Ver Encuestas Disponibles
                </button>
              </div>
              <div class="col-md-4 mb-3">
                <button 
                  class="btn btn-success w-100"
                  @click="navigateTo('student-respond')"
                >
                  <i class="fas fa-edit me-2"></i>
                  Responder Encuesta
                </button>
              </div>
              <div class="col-md-4 mb-3">
                <button 
                  class="btn btn-info w-100"
                  @click="navigateTo('student-dashboard')"
                >
                  <i class="fas fa-chart-bar me-2"></i>
                  Mi Progreso
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.student-dashboard {
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

.student-info p {
  margin-bottom: 0.5rem;
  color: #495057;
}

.student-info strong {
  color: #333;
  font-weight: 600;
}

.table-custom th {
  border-top: none;
  font-weight: 600;
  color: #495057;
}

.table-custom td {
  vertical-align: middle;
}

.stars {
  display: flex;
  gap: 2px;
}

.stars i {
  font-size: 0.8rem;
}

.btn {
  border-radius: 8px;
  font-weight: 500;
  transition: all 0.3s ease;
}

.btn:hover {
  transform: translateY(-1px);
}

.text-primary {
  color: #667eea !important;
}

.text-success {
  color: #28a745 !important;
}

.text-warning {
  color: #ffc107 !important;
}

@media (max-width: 768px) {
  .student-dashboard {
    padding: 1rem;
  }
  
  .table-responsive {
    font-size: 0.9rem;
  }
}
</style> 