<script setup>
import { useAuthStore } from '../stores/auth';
const authStore = useAuthStore();

const logout = () => {
  authStore.logout();
};
</script>

<template>
<nav class="navbar navbar-expand-lg bg-success">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">Carros</a>
    <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="Toggle navigation">
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarSupportedContent">
      <ul class="navbar-nav me-auto mb-2 mb-lg-0" v-if="authStore.user">
        <li class="nav-item px-lg-5 h4">
          {{ authStore.user.name }}
        </li>
        <li class="nav-item px-lg-5">
          <RouterLink to="/marcas" class="nav-link">Marcas</RouterLink>
        </li>
        <li class="nav-item px-lg-5">
          <RouterLink to="/autos" class="nav-link">Autos</RouterLink>
        </li>
        <li class="nav-item px-lg-5">
          <RouterLink to="/graphic" class="nav-link">Graphic</RouterLink>
        </li>
        <li class="nav-item px-lg-5">
          <RouterLink to="/reports" class="nav-link">Reports</RouterLink>
        </li>
      </ul>
      <ul class="navbar-nav me-auto mb-2 mb-lg-0" v-if="authStore.user">
        <li class="nav-item px-lg-5">
          <button @click="logout" class="btn btn-success">Salir</button>
        </li>
      </ul>
    </div>
  </div>
</nav>
</template>
