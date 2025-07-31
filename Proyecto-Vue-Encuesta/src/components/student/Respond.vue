<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  user: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['navigate'])

const currentStep = ref(0)
const responses = ref({})

const survey = ref({
  id: 1,
  titulo: 'Satisfacción Estudiantil 2024',
  descripcion: 'Encuesta para evaluar la satisfacción general de los estudiantes',
  preguntas: [
    {
      id: 1,
      pregunta: '¿Qué tan satisfecho estás con la calidad de la enseñanza?',
      tipo: 'radio',
      opciones: ['Muy insatisfecho', 'Insatisfecho', 'Neutral', 'Satisfecho', 'Muy satisfecho'],
      requerida: true
    },
    {
      id: 2,
      pregunta: '¿Qué facultad cursas?',
      tipo: 'select',
      opciones: ['Ciencias', 'Ingeniería', 'Medicina', 'Sociales', 'Educación'],
      requerida: true
    },
    {
      id: 3,
      pregunta: '¿Qué aspectos mejorarías de la universidad?',
      tipo: 'textarea',
      requerida: false
    },
    {
      id: 4,
      pregunta: '¿En qué semestre te encuentras?',
      tipo: 'number',
      requerida: true
    }
  ]
})

const currentQuestion = computed(() => survey.value.preguntas[currentStep.value])
const isLastQuestion = computed(() => currentStep.value === survey.value.preguntas.length - 1)
const progress = computed(() => ((currentStep.value + 1) / survey.value.preguntas.length) * 100)

const navigateTo = (view) => {
  emit('navigate', view)
}

const nextQuestion = () => {
  if (currentStep.value < survey.value.preguntas.length - 1) {
    currentStep.value++
  } else {
    submitSurvey()
  }
}

const previousQuestion = () => {
  if (currentStep.value > 0) {
    currentStep.value--
  }
}

const submitSurvey = () => {
  console.log('Encuesta enviada:', responses.value)
  alert('¡Encuesta completada exitosamente!')
  navigateTo('student-dashboard')
}

const updateResponse = (questionId, value) => {
  responses.value[questionId] = value
}
</script>

<template>
  <div class="student-respond">
    <div class="d-flex justify-content-between align-items-center mb-4">
      <h2>{{ survey.titulo }}</h2>
      <button class="btn btn-secondary" @click="navigateTo('student-dashboard')">
        <i class="fas fa-times me-2"></i>
        Cancelar
      </button>
    </div>

    <div class="card">
      <div class="card-body">
        <!-- Progress Bar -->
        <div class="progress-container mb-4">
          <div class="progress">
            <div 
              class="progress-bar" 
              :style="{ width: progress + '%' }"
              role="progressbar"
            ></div>
          </div>
          <div class="text-center mt-2">
            <small class="text-muted">{{ currentStep + 1 }} de {{ survey.preguntas.length }}</small>
          </div>
        </div>

        <!-- Question -->
        <div class="question-container">
          <h4 class="question-title">
            {{ currentQuestion.pregunta }}
            <span v-if="currentQuestion.requerida" class="text-danger">*</span>
          </h4>

          <!-- Radio buttons -->
          <div v-if="currentQuestion.tipo === 'radio'" class="options-container">
            <div 
              v-for="option in currentQuestion.opciones" 
              :key="option"
              class="form-check"
            >
              <input 
                class="form-check-input" 
                type="radio" 
                :name="'question-' + currentQuestion.id"
                :id="'option-' + option"
                :value="option"
                :checked="responses[currentQuestion.id] === option"
                @change="updateResponse(currentQuestion.id, option)"
              >
              <label class="form-check-label" :for="'option-' + option">
                {{ option }}
              </label>
            </div>
          </div>

          <!-- Select -->
          <div v-else-if="currentQuestion.tipo === 'select'" class="options-container">
            <select 
              class="form-select"
              :value="responses[currentQuestion.id]"
              @change="updateResponse(currentQuestion.id, $event.target.value)"
            >
              <option value="">Selecciona una opción</option>
              <option 
                v-for="option in currentQuestion.opciones" 
                :key="option"
                :value="option"
              >
                {{ option }}
              </option>
            </select>
          </div>

          <!-- Textarea -->
          <div v-else-if="currentQuestion.tipo === 'textarea'" class="options-container">
            <textarea 
              class="form-control"
              rows="4"
              :value="responses[currentQuestion.id]"
              @input="updateResponse(currentQuestion.id, $event.target.value)"
              placeholder="Escribe tu respuesta aquí..."
            ></textarea>
          </div>

          <!-- Number -->
          <div v-else-if="currentQuestion.tipo === 'number'" class="options-container">
            <input 
              type="number"
              class="form-control"
              :value="responses[currentQuestion.id]"
              @input="updateResponse(currentQuestion.id, $event.target.value)"
              placeholder="Ingresa un número"
            >
          </div>
        </div>

        <!-- Navigation -->
        <div class="navigation-buttons mt-4">
          <button 
            v-if="currentStep > 0"
            class="btn btn-outline-secondary"
            @click="previousQuestion"
          >
            <i class="fas fa-arrow-left me-2"></i>
            Anterior
          </button>
          
          <button 
            class="btn btn-primary"
            @click="nextQuestion"
            :disabled="currentQuestion.requerida && !responses[currentQuestion.id]"
          >
            {{ isLastQuestion ? 'Enviar Encuesta' : 'Siguiente' }}
            <i v-if="!isLastQuestion" class="fas fa-arrow-right ms-2"></i>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.student-respond {
  padding: 2rem;
}

.card {
  border: none;
  border-radius: 15px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}

.progress {
  height: 8px;
  border-radius: 4px;
}

.progress-bar {
  background: linear-gradient(90deg, #667eea, #764ba2);
  transition: width 0.3s ease;
}

.question-container {
  padding: 2rem 0;
}

.question-title {
  color: #333;
  font-weight: 600;
  margin-bottom: 1.5rem;
}

.options-container {
  margin-top: 1rem;
}

.form-check {
  margin-bottom: 1rem;
  padding: 0.75rem;
  border: 2px solid #e9ecef;
  border-radius: 8px;
  transition: border-color 0.3s ease;
}

.form-check:hover {
  border-color: #667eea;
}

.form-check-input:checked + .form-check-label {
  font-weight: 600;
  color: #667eea;
}

.form-select,
.form-control {
  border: 2px solid #e9ecef;
  border-radius: 8px;
  transition: border-color 0.3s ease;
}

.form-select:focus,
.form-control:focus {
  border-color: #667eea;
  box-shadow: 0 0 0 0.2rem rgba(102, 126, 234, 0.25);
}

.navigation-buttons {
  display: flex;
  justify-content: space-between;
  gap: 1rem;
}

.btn {
  border-radius: 8px;
  font-weight: 500;
  transition: all 0.3s ease;
  min-width: 120px;
}

.btn:hover:not(:disabled) {
  transform: translateY(-1px);
}

.btn:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

@media (max-width: 768px) {
  .student-respond {
    padding: 1rem;
  }
  
  .navigation-buttons {
    flex-direction: column;
  }
  
  .btn {
    width: 100%;
  }
}
</style> 