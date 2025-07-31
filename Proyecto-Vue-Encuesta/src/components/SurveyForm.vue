<script setup>
import { ref, computed } from 'vue'

const props = defineProps({
  questions: {
    type: Array,
    required: true
  }
})

const emit = defineEmits(['submit'])

const responses = ref({})
const currentStep = ref(0)
const errors = ref({})

const currentQuestion = computed(() => props.questions[currentStep.value])
const isLastQuestion = computed(() => currentStep.value === props.questions.length - 1)
const progress = computed(() => ((currentStep.value + 1) / props.questions.length) * 100)

const validateCurrentQuestion = () => {
  const question = currentQuestion.value
  const answer = responses.value[question.id]
  
  if (question.required && (!answer || answer === '')) {
    errors.value[question.id] = 'Esta pregunta es obligatoria'
    return false
  }
  
  delete errors.value[question.id]
  return true
}

const nextQuestion = () => {
  if (validateCurrentQuestion()) {
    if (isLastQuestion.value) {
      submitSurvey()
    } else {
      currentStep.value++
    }
  }
}

const previousQuestion = () => {
  if (currentStep.value > 0) {
    currentStep.value--
  }
}

const submitSurvey = () => {
  if (validateCurrentQuestion()) {
    emit('submit', responses.value)
  }
}

const updateResponse = (questionId, value) => {
  responses.value[questionId] = value
  if (errors.value[questionId]) {
    delete errors.value[questionId]
  }
}
</script>

<template>
  <div class="survey-form">
    <!-- Progress Bar -->
    <div class="progress-container">
      <div class="progress-bar">
        <div class="progress-fill" :style="{ width: progress + '%' }"></div>
      </div>
      <span class="progress-text">{{ currentStep + 1 }} de {{ questions.length }}</span>
    </div>

    <!-- Question Container -->
    <div class="question-container">
      <div class="question-header">
        <h2 class="question-title">{{ currentQuestion.question }}</h2>
        <span v-if="currentQuestion.required" class="required-badge">* Requerido</span>
      </div>

      <!-- Different input types -->
      <div class="question-input">
        <!-- Number input -->
        <input
          v-if="currentQuestion.type === 'number'"
          type="number"
          :value="responses[currentQuestion.id]"
          @input="updateResponse(currentQuestion.id, $event.target.value)"
          class="input-field"
          placeholder="Ingresa tu respuesta"
        />

        <!-- Select dropdown -->
        <select
          v-else-if="currentQuestion.type === 'select'"
          :value="responses[currentQuestion.id]"
          @change="updateResponse(currentQuestion.id, $event.target.value)"
          class="input-field"
        >
          <option value="">Selecciona una opción</option>
          <option
            v-for="option in currentQuestion.options"
            :key="option"
            :value="option"
          >
            {{ option }}
          </option>
        </select>

        <!-- Radio buttons -->
        <div v-else-if="currentQuestion.type === 'radio'" class="radio-group">
          <label
            v-for="option in currentQuestion.options"
            :key="option"
            class="radio-option"
          >
            <input
              type="radio"
              :name="'question-' + currentQuestion.id"
              :value="option"
              :checked="responses[currentQuestion.id] === option"
              @change="updateResponse(currentQuestion.id, option)"
            />
            <span class="radio-label">{{ option }}</span>
          </label>
        </div>

        <!-- Textarea -->
        <textarea
          v-else-if="currentQuestion.type === 'textarea'"
          :value="responses[currentQuestion.id]"
          @input="updateResponse(currentQuestion.id, $event.target.value)"
          class="input-field textarea"
          placeholder="Escribe tu respuesta aquí..."
          rows="4"
        ></textarea>

        <!-- Text input (default) -->
        <input
          v-else
          type="text"
          :value="responses[currentQuestion.id]"
          @input="updateResponse(currentQuestion.id, $event.target.value)"
          class="input-field"
          placeholder="Ingresa tu respuesta"
        />
      </div>

      <!-- Error message -->
      <div v-if="errors[currentQuestion.id]" class="error-message">
        {{ errors[currentQuestion.id] }}
      </div>
    </div>

    <!-- Navigation buttons -->
    <div class="navigation-buttons">
      <button
        v-if="currentStep > 0"
        @click="previousQuestion"
        class="btn btn-secondary"
      >
        ← Anterior
      </button>
      
      <button
        @click="nextQuestion"
        class="btn btn-primary"
        :disabled="!responses[currentQuestion.id] && currentQuestion.required"
      >
        {{ isLastQuestion ? 'Enviar Encuesta' : 'Siguiente →' }}
      </button>
    </div>
  </div>
</template>

<style scoped>
.survey-form {
  padding: 2rem;
}

.progress-container {
  margin-bottom: 2rem;
  text-align: center;
}

.progress-bar {
  width: 100%;
  height: 8px;
  background-color: #e0e0e0;
  border-radius: 4px;
  overflow: hidden;
  margin-bottom: 0.5rem;
}

.progress-fill {
  height: 100%;
  background: linear-gradient(90deg, #667eea, #764ba2);
  transition: width 0.3s ease;
}

.progress-text {
  font-size: 0.9rem;
  color: #666;
  font-weight: 500;
}

.question-container {
  margin-bottom: 2rem;
}

.question-header {
  display: flex;
  align-items: center;
  gap: 1rem;
  margin-bottom: 1.5rem;
}

.question-title {
  margin: 0;
  font-size: 1.5rem;
  color: #333;
  font-weight: 600;
}

.required-badge {
  background-color: #ff4757;
  color: white;
  padding: 0.25rem 0.75rem;
  border-radius: 20px;
  font-size: 0.8rem;
  font-weight: 500;
}

.question-input {
  margin-bottom: 1rem;
}

.input-field {
  width: 100%;
  padding: 1rem;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  font-size: 1rem;
  transition: border-color 0.3s ease;
  box-sizing: border-box;
}

.input-field:focus {
  outline: none;
  border-color: #667eea;
}

.textarea {
  resize: vertical;
  min-height: 100px;
}

.radio-group {
  display: flex;
  flex-direction: column;
  gap: 0.75rem;
}

.radio-option {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.75rem;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  cursor: pointer;
  transition: border-color 0.3s ease;
}

.radio-option:hover {
  border-color: #667eea;
}

.radio-option input[type="radio"] {
  width: 18px;
  height: 18px;
  accent-color: #667eea;
}

.radio-label {
  font-size: 1rem;
  color: #333;
}

.error-message {
  color: #ff4757;
  font-size: 0.9rem;
  margin-top: 0.5rem;
}

.navigation-buttons {
  display: flex;
  justify-content: space-between;
  gap: 1rem;
}

.btn {
  padding: 0.75rem 1.5rem;
  border: none;
  border-radius: 8px;
  font-size: 1rem;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s ease;
  min-width: 120px;
}

.btn-primary {
  background: linear-gradient(135deg, #667eea, #764ba2);
  color: white;
}

.btn-primary:hover:not(:disabled) {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(102, 126, 234, 0.4);
}

.btn-primary:disabled {
  opacity: 0.6;
  cursor: not-allowed;
}

.btn-secondary {
  background-color: #f8f9fa;
  color: #333;
  border: 2px solid #e0e0e0;
}

.btn-secondary:hover {
  background-color: #e9ecef;
  border-color: #667eea;
}

@media (max-width: 768px) {
  .survey-form {
    padding: 1rem;
  }
  
  .question-title {
    font-size: 1.25rem;
  }
  
  .navigation-buttons {
    flex-direction: column;
  }
  
  .btn {
    width: 100%;
  }
}
</style> 