<script setup>
import { ref, onMounted } from 'vue'
import Login from './components/auth/Login.vue'
import Register from './components/auth/Register.vue'
import AdminDashboard from './components/admin/Dashboard.vue'
import StudentDashboard from './components/student/Dashboard.vue'
import AdminUsers from './components/admin/Users.vue'
import AdminSurveys from './components/admin/Surveys.vue'
import AdminStatistics from './components/admin/Statistics.vue'
import AdminResults from './components/admin/Results.vue'
import StudentSurveys from './components/student/Surveys.vue'
import StudentRespond from './components/student/Respond.vue'
import SurveyResults from './components/SurveyResults.vue'

const currentUser = ref(null)
const currentView = ref('login')
const alertMessage = ref('')
const alertType = ref('')
const showAlertMessage = ref(false)

// Simulación de base de datos de usuarios
const defaultUsers = [
  {
    username: 'admin',
    password: 'admin123',
    role: 'admin',
    name: 'Administrador'
  }
]

// Función para obtener todos los usuarios
const getUsers = () => {
  const storedUsers = JSON.parse(localStorage.getItem('usuarios') || '[]')
  return [...defaultUsers, ...storedUsers]
}

// Función para manejar el inicio de sesión
const handleLogin = (credentials) => {
  const users = getUsers()
  const user = users.find(u => u.username === credentials.username && u.password === credentials.password)
  
  if (user) {
    currentUser.value = {
      username: user.username,
      role: user.role,
      nombre: user.nombre || null,
      apellido: user.apellido || null,
      name: user.name || (user.nombre ? `${user.nombre} ${user.apellido}` : null),
      matricula: user.matricula || null,
      facultad: user.facultad || null,
      carrera: user.carrera || null,
      semestre: user.semestre || null,
      email: user.email || null
    }
    
    sessionStorage.setItem('currentUser', JSON.stringify(currentUser.value))
    
    if (user.role === 'admin') {
      currentView.value = 'admin-dashboard'
    } else {
      currentView.value = 'student-dashboard'
    }
  } else {
    showAlert('Credenciales inválidas. Por favor, intente nuevamente.', 'danger')
  }
}

// Función para manejar el registro (exactamente igual que el original)
const handleRegister = (userData) => {
  // Obtener usuarios existentes
  let usuarios = JSON.parse(localStorage.getItem('usuarios') || '[]')
  
  // Verificar si el usuario ya existe
  if (usuarios.some(u => u.username === userData.username || u.email === userData.email)) {
    showAlert('El usuario o correo electrónico ya está registrado.', 'danger')
    return
  }
  
  // Agregar nuevo usuario
  usuarios.push(userData)
  localStorage.setItem('usuarios', JSON.stringify(usuarios))
  
  // Mostrar mensaje de éxito
  showAlert('Registro exitoso. Será redirigido al inicio de sesión.', 'success')
  
  // Redirigir al login después de 2 segundos (igual que el original)
  setTimeout(() => {
    currentView.value = 'login'
  }, 2000)
}

// Función para cerrar sesión
const logout = () => {
  currentUser.value = null
  sessionStorage.removeItem('currentUser')
  currentView.value = 'login'
}

// Función para mostrar alertas
const showAlert = (message, type) => {
  alertMessage.value = message
  alertType.value = type
  showAlertMessage.value = true
  
  // Auto-ocultar después de 5 segundos
  setTimeout(() => {
    showAlertMessage.value = false
  }, 5000)
}

// Verificar autenticación al cargar
onMounted(() => {
  const storedUser = sessionStorage.getItem('currentUser')
  if (storedUser) {
    currentUser.value = JSON.parse(storedUser)
    if (currentUser.value.role === 'admin') {
      currentView.value = 'admin-dashboard'
    } else {
      currentView.value = 'student-dashboard'
    }
  }
})

// Navegación
const navigateTo = (view) => {
  currentView.value = view
}
</script>

<template>
  <div id="app">
    <!-- Header con navegación -->
    <header v-if="currentUser" class="app-header">
      <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
        <div class="container">
          <a class="navbar-brand" href="#">
            <img src="/src/assets/new-logo-uleam.png" alt="Logo ULEAM" height="40">
            Sistema de Encuestas ULEAM
          </a>
          
          <div class="navbar-nav ms-auto">
            <div class="nav-item dropdown">
              <a class="nav-link dropdown-toggle" href="#" role="button" data-bs-toggle="dropdown">
                <i class="fas fa-user"></i> {{ currentUser.name || currentUser.username }}
              </a>
              <ul class="dropdown-menu">
                <li><a class="dropdown-item" href="#" @click="logout">Cerrar Sesión</a></li>
              </ul>
            </div>
          </div>
        </div>
      </nav>
    </header>

    <!-- Sistema de alertas -->
    <div v-if="showAlertMessage" class="alert-container">
      <div :class="`alert alert-${alertType} alert-dismissible fade show`" role="alert">
        {{ alertMessage }}
        <button type="button" class="btn-close" @click="showAlertMessage = false"></button>
      </div>
    </div>

    <!-- Contenido principal -->
    <main class="app-main">
      <!-- Login -->
      <Login 
        v-if="currentView === 'login'" 
        @login="handleLogin"
        @go-to-register="currentView = 'register'"
      />
      
      <!-- Register -->
      <Register 
        v-else-if="currentView === 'register'" 
        @register="handleRegister"
        @go-to-login="currentView = 'login'"
      />
      
      <!-- Admin Dashboard -->
      <AdminDashboard 
        v-else-if="currentView === 'admin-dashboard'" 
        :user="currentUser"
        @navigate="navigateTo"
      />
      
      <!-- Admin Users -->
      <AdminUsers 
        v-else-if="currentView === 'admin-users'" 
        :user="currentUser"
        @navigate="navigateTo"
      />
      
      <!-- Admin Surveys -->
      <AdminSurveys 
        v-else-if="currentView === 'admin-surveys'" 
        :user="currentUser"
        @navigate="navigateTo"
      />
      
      <!-- Admin Statistics -->
      <AdminStatistics 
        v-else-if="currentView === 'admin-statistics'" 
        :user="currentUser"
        @navigate="navigateTo"
      />
      
      <!-- Admin Results -->
      <AdminResults 
        v-else-if="currentView === 'admin-results'" 
        :user="currentUser"
        @navigate="navigateTo"
      />
      
      <!-- Student Dashboard -->
      <StudentDashboard 
        v-else-if="currentView === 'student-dashboard'" 
        :user="currentUser"
        @navigate="navigateTo"
      />
      
      <!-- Student Surveys -->
      <StudentSurveys 
        v-else-if="currentView === 'student-surveys'" 
        :user="currentUser"
        @navigate="navigateTo"
      />
      
      <!-- Student Respond -->
      <StudentRespond 
        v-else-if="currentView === 'student-respond'" 
        :user="currentUser"
        @navigate="navigateTo"
      />
      
      <!-- Survey Results -->
      <SurveyResults 
        v-else-if="currentView === 'survey-results'" 
        :user="currentUser"
        @navigate="navigateTo"
      />
    </main>
  </div>
</template>

<style scoped>
#app {
  min-height: 100vh;
  background-color: #f8f9fa;
}

.app-header {
  box-shadow: 0 2px 4px rgba(0,0,0,0.1);
}

.app-main {
  padding: 2rem 0;
  max-width: 1400px;
  margin: 0 auto;
}

.navbar-brand img {
  margin-right: 0.5rem;
}

@media (max-width: 768px) {
  .app-main {
    padding: 1rem 0;
    max-width: 100%;
  }
}

@media (min-width: 1400px) {
  .app-main {
    padding: 2rem 1rem;
  }
}

.alert-container {
  position: fixed;
  top: 20px;
  right: 20px;
  z-index: 9999;
  max-width: 400px;
}
</style>
