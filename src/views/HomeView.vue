<script setup>
import { ref, computed } from "vue";
import axios from 'axios';


const errorMessage = ref("");
const user = ref("");
const isLoading = ref(false);
const logged = ref(false);

// URL del endpoint de login
const meUrl = "/v1/auth/me";
// Configuración del cliente Axios
const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_AUTH_URL, // URL base de la API
  headers: {
    "Content-Type": "application/json",
  },
});
// Agregar un interceptor para incluir el token en todas las solicitudes
apiClient.interceptors.request.use((config) => {
  const token = localStorage.getItem('token');
  const tokenType = localStorage.getItem('token_type');

  if (token && tokenType) {
    config.headers.Authorization = `${tokenType} ${token}`;
  }
  return config;
}, (error) => {
  return Promise.reject(error);
});

// Función para obtener el perfil de usuario
const getUserProfile = async () => {
  try {
    const response = await apiClient.get(meUrl); // No es necesario repetir el encabezado aquí
    localStorage.setItem('user',JSON.stringify(response.data));
    logged.value = true;
    user.value = response.data;
    return response.data;
  } catch (error) {
    localStorage.clear();

    // Redirigir al usuario
    window.location.href = "/";
    isLoading.value = false;
    console.error("Error al obtener perfil:", error.response?.data || error.message);
    throw error;
  }
};
getUserProfile();

</script>
<template>
    <div v-if="logged" class="container-xxl">
       Este es Home {{ user.name }}
    </div>
    <div v-else class="container-xxl">
       Espere...
    </div>
</template>

<style lang="css" scoped></style>
