<script setup>
import { ref, computed } from 'vue'

const emit = defineEmits(['register', 'go-to-login'])

// Form data
const formData = ref({
  nombre: '',
  apellido: '',
  facultad: '',
  carrera: '',
  semestre: '',
  email: '',
  username: '',
  password: '',
  confirmPassword: ''
})

const errorMessage = ref('')
const successMessage = ref('')

// Mapeo de facultades a carreras (exactamente igual que el original)
const carrerasPorFacultad = {
  ciencias: [
    'Matemáticas',
    'Física',
    'Química',
    'Biología'
  ],
  ingenieria: [
    'Ingeniería en Sistemas',
    'Ingeniería Civil',
    'Ingeniería Industrial',
    'Ingeniería Eléctrica',
    'Ingeniería Mecánica'
  ],
  medicina: [
    'Medicina',
    'Enfermería',
    'Odontología',
    'Farmacia'
  ],
  sociales: [
    'Psicología',
    'Sociología',
    'Trabajo Social',
    'Comunicación Social'
  ],
  educacion: [
    'Educación Básica',
    'Educación Inicial',
    'Educación Especial',
    'Educación Física'
  ]
}

// Computed property para las carreras disponibles (igual que el original)
const carrerasDisponibles = computed(() => {
  if (!formData.value.facultad) return []
  return carrerasPorFacultad[formData.value.facultad] || []
})

// Función para validar el formulario (exactamente igual que el original)
const validarFormulario = () => {
  const { nombre, apellido, facultad, carrera, semestre, email, username, password, confirmPassword } = formData.value

  // Validar nombre y apellido
  if (nombre.length < 2 || apellido.length < 2) {
    errorMessage.value = 'El nombre y apellido deben tener al menos 2 caracteres.'
    return false
  }

  // Validar selección de facultad y carrera
  if (!facultad || !carrera) {
    errorMessage.value = 'Debe seleccionar una facultad y carrera.'
    return false
  }

  // Validar semestre
  if (!semestre) {
    errorMessage.value = 'Debe seleccionar un semestre.'
    return false
  }

  // Validar email
  const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/
  if (!emailRegex.test(email)) {
    errorMessage.value = 'Por favor, ingrese un correo electrónico válido.'
    return false
  }

  // Validar username
  if (username.length < 4) {
    errorMessage.value = 'El nombre de usuario debe tener al menos 4 caracteres.'
    return false
  }

  // Validar contraseña
  if (password.length < 6) {
    errorMessage.value = 'La contraseña debe tener al menos 6 caracteres.'
    return false
  }

  // Validar confirmación de contraseña
  if (password !== confirmPassword) {
    errorMessage.value = 'Las contraseñas no coinciden.'
    return false
  }

  return true
}

const handleSubmit = () => {
  errorMessage.value = ''
  successMessage.value = ''

  if (!validarFormulario()) {
    return
  }

  // Crear objeto de usuario (igual que el original)
  const nuevoUsuario = {
    nombre: formData.value.nombre,
    apellido: formData.value.apellido,
    facultad: formData.value.facultad,
    carrera: formData.value.carrera,
    semestre: formData.value.semestre,
    email: formData.value.email,
    username: formData.value.username,
    password: formData.value.password,
    role: 'estudiante', // Por defecto, todos los registros son estudiantes
    fechaRegistro: new Date().toISOString()
  }

  // Emitir evento de registro
  emit('register', nuevoUsuario)

  // Limpiar formulario
  Object.keys(formData.value).forEach(key => {
    formData.value[key] = ''
  })
}

const goToLogin = () => {
  emit('go-to-login')
}
</script>

<template>
  <div class="container">
    <div class="row justify-content-center align-items-center min-vh-100">
      <div class="col-md-8 col-lg-6">
        <div class="card shadow-lg">
          <div class="card-body p-5">
            <div class="text-center mb-4">
              <img src="/src/assets/new-logo-uleam.png" alt="Logo ULEAM" height="140">
              <h2 class="text-primary">Registro de Usuario</h2>
              <p class="text-muted">Complete el formulario para crear su cuenta</p>
            </div>

            <!-- Error message -->
            <div v-if="errorMessage" class="alert alert-danger alert-dismissible fade show" role="alert">
              {{ errorMessage }}
              <button type="button" class="btn-close" @click="errorMessage = ''"></button>
            </div>

            <!-- Success message -->
            <div v-if="successMessage" class="alert alert-success alert-dismissible fade show" role="alert">
              {{ successMessage }}
              <button type="button" class="btn-close" @click="successMessage = ''"></button>
            </div>

            <form @submit.prevent="handleSubmit">
              <!-- Información Personal -->
              <div class="row mb-3">
                <div class="col-md-6">
                  <label for="nombre" class="form-label">Nombre</label>
                  <input 
                    type="text" 
                    class="form-control" 
                    id="nombre" 
                    v-model="formData.nombre"
                    required
                  >
                </div>
                <div class="col-md-6">
                  <label for="apellido" class="form-label">Apellido</label>
                  <input 
                    type="text" 
                    class="form-control" 
                    id="apellido" 
                    v-model="formData.apellido"
                    required
                  >
                </div>
              </div>

              <!-- Información Académica -->
              <div class="row mb-3">
                <div class="col-md-6">
                  <label for="facultad" class="form-label">Facultad</label>
                  <select 
                    class="form-select" 
                    id="facultad" 
                    v-model="formData.facultad"
                    required
                  >
                    <option value="">Seleccione una facultad</option>
                    <option value="ciencias">Facultad de Ciencias</option>
                    <option value="ingenieria">Facultad de Ingeniería</option>
                    <option value="medicina">Facultad de Medicina</option>
                    <option value="sociales">Facultad de Ciencias Sociales</option>
                    <option value="educacion">Facultad de Educación</option>
                  </select>
                </div>
                <div class="col-md-6">
                  <label for="carrera" class="form-label">Carrera</label>
                  <select 
                    class="form-select" 
                    id="carrera" 
                    v-model="formData.carrera"
                    required
                  >
                    <option value="">Seleccione una carrera</option>
                    <option 
                      v-for="carrera in carrerasDisponibles" 
                      :key="carrera"
                      :value="carrera.toLowerCase().replace(/\s+/g, '_')"
                    >
                      {{ carrera }}
                    </option>
                  </select>
                </div>
              </div>

              <div class="row mb-3">
                <div class="col-md-6">
                  <label for="semestre" class="form-label">Semestre Actual</label>
                  <select 
                    class="form-select" 
                    id="semestre" 
                    v-model="formData.semestre"
                    required
                  >
                    <option value="">Seleccione el semestre</option>
                    <option value="1">Primer Semestre</option>
                    <option value="2">Segundo Semestre</option>
                    <option value="3">Tercer Semestre</option>
                    <option value="4">Cuarto Semestre</option>
                    <option value="5">Quinto Semestre</option>
                    <option value="6">Sexto Semestre</option>
                    <option value="7">Séptimo Semestre</option>
                    <option value="8">Octavo Semestre</option>
                    <option value="9">Noveno Semestre</option>
                    <option value="10">Décimo Semestre</option>
                  </select>
                </div>
              </div>

              <!-- Información de Cuenta -->
              <div class="row mb-3">
                <div class="col-md-6">
                  <label for="email" class="form-label">Correo Electrónico</label>
                  <input 
                    type="email" 
                    class="form-control" 
                    id="email" 
                    v-model="formData.email"
                    required
                  >
                </div>
                <div class="col-md-6">
                  <label for="username" class="form-label">Nombre de Usuario</label>
                  <input 
                    type="text" 
                    class="form-control" 
                    id="username" 
                    v-model="formData.username"
                    required
                  >
                </div>
              </div>

              <div class="row mb-4">
                <div class="col-md-6">
                  <label for="password" class="form-label">Contraseña</label>
                  <input 
                    type="password" 
                    class="form-control" 
                    id="password" 
                    v-model="formData.password"
                    required
                  >
                </div>
                <div class="col-md-6">
                  <label for="confirmPassword" class="form-label">Confirmar Contraseña</label>
                  <input 
                    type="password" 
                    class="form-control" 
                    id="confirmPassword" 
                    v-model="formData.confirmPassword"
                    required
                  >
                </div>
              </div>

              <div class="d-grid gap-2">
                <button type="submit" class="btn btn-primary btn-lg">
                  Registrar Cuenta
                </button>
                <button 
                  type="button" 
                  class="btn btn-outline-secondary"
                  @click="goToLogin"
                >
                  Volver al Inicio de Sesión
                </button>
              </div>
            </form>
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

.btn-outline-secondary {
  border-color: #6c757d;
  color: #6c757d;
}

.btn-outline-secondary:hover {
  background-color: #6c757d;
  border-color: #6c757d;
}

.form-control:focus,
.form-select:focus {
  border-color: #667eea;
  box-shadow: 0 0 0 0.2rem rgba(102, 126, 234, 0.25);
}

@media (max-width: 768px) {
  .card-body {
    padding: 2rem !important;
  }
}
</style> 