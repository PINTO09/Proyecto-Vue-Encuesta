<script setup>
import { ref } from 'vue'

const emit = defineEmits(['login', 'go-to-register'])

const username = ref('')
const password = ref('')
const errorMessage = ref('')

const handleSubmit = () => {
  if (!username.value || !password.value) {
    errorMessage.value = 'Por favor, complete todos los campos.'
    return
  }
  
  emit('login', {
    username: username.value,
    password: password.value
  })
  
  // Limpiar campos
  username.value = ''
  password.value = ''
  errorMessage.value = ''
}

const goToRegister = () => {
  emit('go-to-register')
}
</script>

<template>
  <div class="container">
    <div class="row justify-content-center align-items-center min-vh-100">
      <div class="col-md-6 col-lg-4">
        <div class="card shadow-lg">
          <div class="card-body p-5">
            <div class="text-center mb-4">
              <img src="/src/assets/new-logo-uleam.png" alt="Logo ULEAM" height="140">
            </div>
            <h2 class="text-center mb-4">Sistema de Encuestas</h2>
            <p class="text-center text-muted">Inicie sesión para continuar</p>
            
            <!-- Error message -->
            <div v-if="errorMessage" class="alert alert-danger alert-dismissible fade show" role="alert">
              {{ errorMessage }}
              <button type="button" class="btn-close" @click="errorMessage = ''"></button>
            </div>
            
            <form @submit.prevent="handleSubmit">
              <div class="mb-3">
                <label for="username" class="form-label">Usuario</label>
                <div class="input-group">
                  <span class="input-group-text"><i class="fas fa-user"></i></span>
                  <input 
                    type="text" 
                    class="form-control" 
                    id="username" 
                    v-model="username"
                    required
                  >
                </div>
              </div>
              <div class="mb-4">
                <label for="password" class="form-label">Contraseña</label>
                <div class="input-group">
                  <span class="input-group-text"><i class="fas fa-lock"></i></span>
                  <input 
                    type="password" 
                    class="form-control" 
                    id="password" 
                    v-model="password"
                    required
                  >
                </div>
              </div>
              <div class="d-grid gap-2">
                <button type="submit" class="btn btn-primary btn-lg">
                  Iniciar Sesión
                </button>
                <button 
                  type="button" 
                  class="btn btn-outline-primary"
                  @click="goToRegister"
                >
                  <i class="fas fa-user-plus"></i> Crear Nueva Cuenta
                </button>
              </div>
            </form>
            <div class="text-center mt-3">
              <a href="#" class="text-muted">¿Olvidó su contraseña?</a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.card {
  border: none;
  border-radius: 15px;
}

.card-body {
  padding: 3rem !important;
}

.btn-primary {
  background: linear-gradient(135deg, #667eea, #764ba2);
  border: none;
  padding: 0.75rem 1.5rem;
  font-weight: 500;
}

.btn-primary:hover {
  background: linear-gradient(135deg, #5a6fd8, #6a4190);
  transform: translateY(-1px);
}

.btn-outline-primary {
  border-color: #667eea;
  color: #667eea;
}

.btn-outline-primary:hover {
  background-color: #667eea;
  border-color: #667eea;
}

.input-group-text {
  background-color: #f8f9fa;
  border-color: #dee2e6;
}

.form-control:focus {
  border-color: #667eea;
  box-shadow: 0 0 0 0.2rem rgba(102, 126, 234, 0.25);
}

@media (max-width: 768px) {
  .card-body {
    padding: 2rem !important;
  }
}
</style> 