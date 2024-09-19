<template>
  <header class="bg-gradient-to-r from-fuchsia-400 to-cyan-500 py-4">
    <div
      class="container mx-auto flex flex-col md:flex-row justify-between items-center"
    >
      <nav class="text-center md:text-left">
        <router-link to="/listprestamo" class="text-white hover:underline mx-4">
          Lista de Préstamos
        </router-link>
        <router-link to="/listcliente" class="text-white hover:underline mx-4">
          Lista de Clientes
        </router-link>

        <!-- <router-link to="/preclases" class="text-white hover:underline mx-4">
          <i class="fas fa-user-circle mr-2"></i>
          <span class="font-bold">Ingresar</span>
        </router-link> -->
      </nav>
    </div>
  </header>
</template>

<script>
export default {
  name: 'Header',
  props: {},
};
</script>
