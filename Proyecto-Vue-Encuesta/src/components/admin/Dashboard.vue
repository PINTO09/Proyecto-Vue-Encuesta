<script setup>
import { ref, onMounted } from 'vue'

const props = defineProps({
  user: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['navigate'])

// Datos de estadísticas
const stats = ref({
  totalEncuestas: 24,
  encuestasActivas: 12,
  totalUsuarios: 1234,
  estudiantes: 1200,
  totalRespuestas: 8567,
  respuestasMes: 1234,
  participacion: 85
})

// Datos de encuestas recientes
const recentSurveys = ref([
  {
    id: 1,
    titulo: 'Satisfacción Estudiantil',
    estado: 'Activa',
    respuestas: 156,
    fecha: '2024-01-15'
  },
  {
    id: 2,
    titulo: 'Evaluación Docente',
    estado: 'Activa',
    respuestas: 89,
    fecha: '2024-01-14'
  },
  {
    id: 3,
    titulo: 'Infraestructura Universitaria',
    estado: 'Cerrada',
    respuestas: 234,
    fecha: '2024-01-10'
  },
  {
    id: 4,
    titulo: 'Servicios Estudiantiles',
    estado: 'Activa',
    respuestas: 67,
    fecha: '2024-01-12'
  }
])

// Datos para el gráfico de participación por facultad
const participationData = ref({
  labels: ['Ciencias', 'Ingeniería', 'Medicina', 'Sociales', 'Educación'],
  datasets: [{
    label: 'Participación (%)',
    data: [78, 85, 92, 76, 88],
    backgroundColor: [
      'rgba(255, 99, 132, 0.8)',
      'rgba(54, 162, 235, 0.8)',
      'rgba(255, 206, 86, 0.8)',
      'rgba(75, 192, 192, 0.8)',
      'rgba(153, 102, 255, 0.8)'
    ],
    borderColor: [
      'rgba(255, 99, 132, 1)',
      'rgba(54, 162, 235, 1)',
      'rgba(255, 206, 86, 1)',
      'rgba(75, 192, 192, 1)',
      'rgba(153, 102, 255, 1)'
    ],
    borderWidth: 2
  }]
})

const navigateTo = (view) => {
  emit('navigate', view)
}

onMounted(() => {
  // Aquí se podría inicializar Chart.js si fuera necesario
  console.log('Dashboard del administrador cargado')
})
</script>

<template>
  <div class="admin-dashboard">
    <div class="container-fluid">
      <!-- Tarjetas de Resumen -->
    <div class="row mb-4">
      <div class="col-md-3">
        <div class="card stats-card">
          <div class="card-body">
            <h5 class="card-title">Total Encuestas</h5>
            <h2 class="card-text">{{ stats.totalEncuestas }}</h2>
            <p class="card-text"><small>Activas: {{ stats.encuestasActivas }}</small></p>
          </div>
        </div>
      </div>
      <div class="col-md-3">
        <div class="card stats-card">
          <div class="card-body">
            <h5 class="card-title">Usuarios</h5>
            <h2 class="card-text">{{ stats.totalUsuarios.toLocaleString() }}</h2>
            <p class="card-text"><small>Estudiantes: {{ stats.estudiantes.toLocaleString() }}</small></p>
          </div>
        </div>
      </div>
      <div class="col-md-3">
        <div class="card stats-card">
          <div class="card-body">
            <h5 class="card-title">Respuestas</h5>
            <h2 class="card-text">{{ stats.totalRespuestas.toLocaleString() }}</h2>
            <p class="card-text"><small>Este mes: {{ stats.respuestasMes.toLocaleString() }}</small></p>
          </div>
        </div>
      </div>
      <div class="col-md-3">
        <div class="card stats-card">
          <div class="card-body">
            <h5 class="card-title">Participación</h5>
            <h2 class="card-text">{{ stats.participacion }}%</h2>
            <p class="card-text"><small>Promedio general</small></p>
          </div>
        </div>
      </div>
    </div>

    <!-- Gráficos y Tablas -->
    <div class="row">
      <!-- Gráfico de Participación -->
      <div class="col-md-6 mb-4">
        <div class="card">
          <div class="card-header">
            <h5 class="card-title mb-0">Participación por Facultad</h5>
          </div>
          <div class="card-body">
            <div class="participation-chart">
              <div class="chart-container">
                <div 
                  v-for="(facultad, index) in participationData.labels" 
                  :key="facultad"
                  class="facultad-bar"
                >
                  <div class="facultad-label">{{ facultad }}</div>
                  <div class="bar-container">
                    <div 
                      class="bar-fill"
                      :style="{ width: participationData.datasets[0].data[index] + '%' }"
                    ></div>
                    <span class="bar-value">{{ participationData.datasets[0].data[index] }}%</span>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Últimas Encuestas -->
      <div class="col-md-6 mb-4">
        <div class="card">
          <div class="card-header d-flex justify-content-between align-items-center">
            <h5 class="card-title mb-0">Últimas Encuestas</h5>
            <button 
              class="btn btn-primary btn-sm"
              @click="navigateTo('admin-surveys')"
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
                    <th>Estado</th>
                    <th>Respuestas</th>
                    <th>Acciones</th>
                  </tr>
                </thead>
                <tbody>
                  <tr v-for="encuesta in recentSurveys" :key="encuesta.id">
                    <td>{{ encuesta.titulo }}</td>
                    <td>
                      <span 
                        :class="encuesta.estado === 'Activa' ? 'badge bg-success' : 'badge bg-secondary'"
                      >
                        {{ encuesta.estado }}
                      </span>
                    </td>
                    <td>{{ encuesta.respuestas }}</td>
                    <td>
                      <button class="btn btn-sm btn-outline-primary me-1">
                        <i class="fas fa-eye"></i>
                      </button>
                      <button class="btn btn-sm btn-outline-secondary">
                        <i class="fas fa-edit"></i>
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
    <div class="row">
      <div class="col-12">
        <div class="card">
          <div class="card-header">
            <h5 class="card-title mb-0">Acciones Rápidas</h5>
          </div>
          <div class="card-body">
            <div class="row">
              <div class="col-md-3 mb-3">
                <button 
                  class="btn btn-primary w-100"
                  @click="navigateTo('admin-surveys')"
                >
                  <i class="fas fa-plus-circle me-2"></i>
                  Crear Nueva Encuesta
                </button>
              </div>
              <div class="col-md-3 mb-3">
                <button 
                  class="btn btn-success w-100"
                  @click="navigateTo('admin-users')"
                >
                  <i class="fas fa-users me-2"></i>
                  Gestionar Usuarios
                </button>
              </div>
              <div class="col-md-3 mb-3">
                <button 
                  class="btn btn-info w-100"
                  @click="navigateTo('admin-results')"
                >
                  <i class="fas fa-chart-bar me-2"></i>
                  Ver Resultados
                </button>
              </div>
              <div class="col-md-3 mb-3">
                <button 
                  class="btn btn-warning w-100"
                  @click="navigateTo('admin-statistics')"
                >
                  <i class="fas fa-chart-line me-2"></i>
                  Estadísticas
                </button>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
    </div>
  </div>
</template>

<style scoped>
.admin-dashboard {
  padding: 2rem;
}

.stats-card {
  border: none;
  border-radius: 15px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease;
}

.stats-card:hover {
  transform: translateY(-5px);
}

.stats-card .card-title {
  color: #6c757d;
  font-size: 0.9rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.stats-card .card-text {
  color: #333;
  font-weight: 700;
  margin-bottom: 0.5rem;
}

.stats-card small {
  color: #6c757d;
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

.table-custom th {
  border-top: none;
  font-weight: 600;
  color: #495057;
}

.table-custom td {
  vertical-align: middle;
}

.participation-chart {
  padding: 1rem 0;
}

.chart-container {
  display: flex;
  flex-direction: column;
  gap: 1rem;
}

.facultad-bar {
  display: flex;
  align-items: center;
  gap: 1rem;
}

.facultad-label {
  min-width: 100px;
  font-weight: 500;
  color: #495057;
}

.bar-container {
  flex: 1;
  display: flex;
  align-items: center;
  gap: 0.5rem;
}

.bar-fill {
  height: 20px;
  background: linear-gradient(90deg, #667eea, #764ba2);
  border-radius: 10px;
  transition: width 0.3s ease;
}

.bar-value {
  min-width: 40px;
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
  .admin-dashboard {
    padding: 1rem;
  }
  
  .facultad-label {
    min-width: 80px;
    font-size: 0.9rem;
  }
  
  .bar-fill {
    height: 16px;
  }
}

@media (min-width: 1200px) {
  .admin-dashboard {
    padding: 2rem;
  }
  
  .stats-card {
    margin-bottom: 1.5rem;
  }
  
  .row {
    margin-left: -1rem;
    margin-right: -1rem;
  }
  
  .col-md-3, .col-md-6, .col-md-4 {
    padding-left: 1rem;
    padding-right: 1rem;
  }
}
</style> 