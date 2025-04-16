<script setup>
import { ref, computed } from "vue";
import axios from "axios";

// Variable reactiva para controlar el estado del icono
const isPasswordVisible = ref(false);

const inputType = computed(() =>
  isPasswordVisible.value ? "text" : "password"
);

const iconClass = computed(() =>
  isPasswordVisible.value ? "bx bx-show" : "bx bx-hide"
);

const togglePasswordVisibility = () => {
  isPasswordVisible.value = !isPasswordVisible.value;
};

// Variables reactivas para el formulario y mensajes de error
const email = ref("");
const password = ref("");
const errorMessage = ref("");
const isLoading = ref(false);

// URL del endpoint de Login
const loginUrl = import.meta.env.VITE_API_AUTH_URL + "/v1/auth/login";

// Función de login
const login = async () => {
  isLoading.value = true;
  errorMessage.value = ""; // Limpiar mensaje de error

  try {
    const response = await axios.post(loginUrl, {
      email: email.value,
      password: password.value,
    });

    // Guardar el token en LocalStorage
    const { access_token, token_type } = response.data;
    localStorage.setItem("token", access_token);
    localStorage.setItem("token_type", token_type);

    // Redirigir al usuario
    window.location.href = "/home";
  } catch (error) {
    if (error.response) {
      errorMessage.value =
        error.response.data.error || "Error en el inicio de sesión";
    } else {
      errorMessage.value = error.message || "Error en la conexión";
    }
  } finally {
    isLoading.value = false;
  }
};
</script>


<template>
  <div class="container-xxl">
    <div class="authentication-wrapper authentication-basic container-p-y">
      <div class="authentication-inner">
        <!-- Login -->
        <div class="card">
          <div class="card-body">
            <!-- Logo -->
            <div class="app-brand justify-content-center">
              <span class="app-brand-logo demo">
                <img src="/assets/img/TES_color.png" width="150" />
              </span>
            </div>
            <!-- Logo -->

            <h2 class="mb-3 text-center fw-bold">Bienvenid@ 👋</h2>

            <form class="mb-3" method="POST" @submit.prevent="login">
              <div class="mb-3">
                <label for="email" class="form-label">Correo electrónico</label>
                <input
                  v-model="email"
                  required
                  type="email"
                  class="form-control"
                  id="email"
                  name="email"
                  placeholder="Ingresa tu correo electrónico"
                  autofocus
                />
              </div>

              <div class="mb-3 form-password-toggle">
                <div class="d-flex justify-content-between">
                  <label class="form-label" for="password">Contraseña</label>
                  <a href="#">
                    <small>¿Olvidaste tu contraseña?</small>
                  </a>
                </div>
                <div class="input-group input-group-merge">
                  <input
                    :type="inputType"
                    id="password"
                    v-model="password"
                    class="form-control"
                    name="password"
                    required
                    placeholder="••••••••"
                    aria-describedby="password"
                  />
                  <span
                    class="input-group-text cursor-pointer"
                    @click="togglePasswordVisibility"
                  >
                    <i :class="iconClass"></i>
                  </span>
                </div>
              </div>
              <div class="mb-3">
                <button
                  class="btn btn-primary d-flex justify-content-center align-items-center w-100 py-2"
                  type="submit"
                  :disabled="isLoading"
                >
                  <!-- Spinner -->
                  <span
                    v-if="isLoading"
                    class="spinner-border text-light"
                    role="status"
                    aria-hidden="true"
                  ></span>

                  <!-- Texto -->
                  <span v-if="!isLoading" class="font-bold"> Ingresar </span>
                </button>
              </div>

              <div
                v-if="errorMessage"
                class="mb-3 alert alert-danger alert-dismissible"
                role="alert"
              >
                {{ errorMessage }}
                <button
                  type="button"
                  class="btn-close"
                  data-bs-dismiss="alert"
                  aria-label="Close"
                ></button>
              </div>
            </form>


            <!-- Enlace para redirigir a /register -->
            <!-- <p class="text-center">
              <span>¿Aún no tienes cuenta?</span>
              <router-link to="/register">
                <span> Crea una gratis</span>
              </router-link>
            </p> -->


          </div>
        </div>
        <!-- Register -->
      </div>
    </div>
  </div>
</template>

<style lang="css" scoped>

</style>
