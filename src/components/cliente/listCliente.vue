<template>
  <div class="p-6 max-w-full mx-auto">
    <!-- Formulario para agregar un cliente -->
    <h1 class="text-2xl font-bold mb-4">Agregar Cliente</h1>
    <form @submit.prevent="addCliente" class="mb-6">
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <div class="mb-4">
          <label for="nombre" class="block text-sm font-medium text-gray-700">
            Nombre del Cliente
          </label>
          <input
            id="nombre"
            v-model="newCliente.nombre"
            type="text"
            placeholder="Nombre del cliente"
            required
            class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
          />
        </div>
        <div class="mb-4">
          <label for="contacto" class="block text-sm font-medium text-gray-700">
            Contacto
          </label>
          <input
            id="contacto"
            v-model="newCliente.contacto"
            type="text"
            placeholder="Teléfono o correo"
            required
            class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
          />
        </div>
      </div>
      <button
        type="submit"
        class="inline-flex items-center px-4 py-2 border border-transparent rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
      >
        Agregar Cliente
      </button>
    </form>

    <!-- Tabla de clientes -->
    <h1 class="text-2xl font-bold mb-4">Lista de Clientes</h1>
    <table class="min-w-full bg-white rounded-md shadow-md">
      <thead>
        <tr class="bg-gray-100">
          <th class="py-2 px-4 border">Nombre</th>
          <th class="py-2 px-4 border">Contacto</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="cliente in clientes"
          :key="cliente.id"
          class="text-center border-t"
        >
          <td class="py-2 px-4 border">{{ cliente.nombre }}</td>
          <td class="py-2 px-4 border">{{ cliente.contacto }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
export default {
  data() {
    return {
      BASE_URL:
        'https://firestore.googleapis.com/v1/projects/gustavosuarez-2023/databases/(default)/documents',
      API_KEY: 'YvrRcWQ8Bdt5PNg7SCq1PfQZwVP1TGK0LSafI8ol',
      clientes: [],
      newCliente: {
        nombre: '',
        contacto: '',
      },
    };
  },
  methods: {
    async fetchClientes() {
      try {
        const response = await fetch(
          `${this.BASE_URL}/clientes?key=${this.API_KEY}`
        );
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const data = await response.json();
        if (data.documents) {
          this.clientes = data.documents.map((doc) => ({
            id: doc.name.split('/').pop(),
            nombre: doc.fields.nombre.stringValue || 'Sin nombre',
            contacto: doc.fields.contacto.stringValue || 'Sin contacto',
          }));
        } else {
          this.clientes = [];
        }
      } catch (error) {
        console.error('Error fetching clientes:', error);
      }
    },
    async addCliente() {
      const newDoc = {
        fields: {
          nombre: { stringValue: this.newCliente.nombre },
          contacto: { stringValue: this.newCliente.contacto },
        },
      };

      try {
        const response = await fetch(
          `${this.BASE_URL}/clientes?key=${this.API_KEY}`,
          {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json',
            },
            body: JSON.stringify(newDoc),
          }
        );

        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        // Agregar el cliente a la lista local después de una respuesta exitosa
        this.clientes.push({ ...this.newCliente, id: Date.now() });

        // Limpiar el formulario
        this.newCliente = { nombre: '', contacto: '' };
      } catch (error) {
        console.error('Error adding cliente:', error);
      }
    },
  },
  mounted() {
    // Carga los clientes cuando el componente se monta
    this.fetchClientes();
  },
};
</script>

<style scoped>
/* Puedes agregar estilos adicionales aquí si es necesario */
</style>
