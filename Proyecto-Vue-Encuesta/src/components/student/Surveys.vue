<script setup>
import { ref } from 'vue'

const props = defineProps({
  user: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['navigate'])

const surveys = ref([
  {
    id: 1,
    titulo: 'Satisfacción Estudiantil 2024',
    descripcion: 'Encuesta para evaluar la satisfacción general de los estudiantes',
    fechaLimite: '2024-02-15',
    estado: 'Disponible',
    tiempoEstimado: '10 minutos'
  },
  {
    id: 2,
    titulo: 'Evaluación de Infraestructura',
    descripcion: 'Evaluación de las instalaciones y servicios universitarios',
    fechaLimite: '2024-02-20',
    estado: 'Disponible',
    tiempoEstimado: '15 minutos'
  },
  {
    id: 3,
    titulo: 'Calidad de la Enseñanza',
    descripcion: 'Evaluación de la calidad de la enseñanza y metodologías',
    fechaLimite: '2024-02-25',
    estado: 'Disponible',
    tiempoEstimado: '12 minutos'
  }
])

const navigateTo = (view) => {
  emit('navigate', view)
}

const startSurvey = (surveyId) => {
  console.log('Iniciando encuesta:', surveyId)
  navigateTo('student-respond')
}
</script>

<template>
  <div class="student-surveys">
    <div class="d-flex justify-content-between align-items-center mb-4">
      <h2>Encuestas Disponibles</h2>
      <button class="btn btn-primary" @click="navigateTo('student-dashboard')">
        <i class="fas fa-arrow-left me-2"></i>
        Volver al Dashboard
      </button>
    </div>

    <div class="row">
      <div v-for="survey in surveys" :key="survey.id" class="col-md-6 col-lg-4 mb-4">
        <div class="card survey-card">
          <div class="card-body">
            <h5 class="card-title">{{ survey.titulo }}</h5>
            <p class="card-text">{{ survey.descripcion }}</p>
            <div class="survey-info">
              <p><strong>Fecha límite:</strong> {{ new Date(survey.fechaLimite).toLocaleDateString() }}</p>
              <p><strong>Tiempo estimado:</strong> {{ survey.tiempoEstimado }}</p>
              <p><strong>Estado:</strong> 
                <span class="badge bg-success">{{ survey.estado }}</span>
              </p>
            </div>
            <button 
              class="btn btn-primary w-100 mt-3"
              @click="startSurvey(survey.id)"
            >
              <i class="fas fa-play me-2"></i>
              Comenzar Encuesta
            </button>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.student-surveys {
  padding: 2rem;
}

.survey-card {
  border: none;
  border-radius: 15px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease;
  height: 100%;
}

.survey-card:hover {
  transform: translateY(-5px);
}

.survey-info p {
  margin-bottom: 0.5rem;
  font-size: 0.9rem;
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
  .student-surveys {
    padding: 1rem;
  }
}
</style> 