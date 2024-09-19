<template>
  <div class="p-6 max-w-full mx-auto">
    <h1 class="text-2xl font-bold mb-4">Agregar Préstamo</h1>
    <form @submit.prevent="addPrestamo" class="mb-6">
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <!-- Selector de Cliente -->
        <div class="mb-4">
          <label for="cliente" class="block text-sm font-medium text-gray-700"
            >Seleccionar Cliente</label
          >
          <select
            id="cliente"
            v-model="newPrestamo.clienteId"
            required
            class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
          >
            <option value="" disabled>Selecciona un cliente</option>
            <option
              v-for="cliente in clientes"
              :key="cliente.id"
              :value="cliente.id"
            >
              {{ cliente.nombre }}
            </option>
          </select>
        </div>

        <!-- Campo de Fecha de Inicio -->
        <div class="mb-4">
          <label
            for="fechaInicio"
            class="block text-sm font-medium text-gray-700"
            >Fecha de Inicio</label
          >
          <input
            id="fechaInicio"
            v-model="newPrestamo.fechaInicio"
            type="date"
            required
            class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
          />
        </div>

        <!-- Campos del préstamo -->
        <div class="mb-4">
          <label for="monto" class="block text-sm font-medium text-gray-700"
            >Monto</label
          >
          <input
            id="monto"
            v-model.number="newPrestamo.monto"
            type="number"
            placeholder="Monto del préstamo"
            required
            class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
          />
        </div>

        <div class="mb-4">
          <label for="plazo" class="block text-sm font-medium text-gray-700"
            >Plazo (meses)</label
          >
          <input
            id="plazo"
            v-model.number="newPrestamo.plazo"
            type="number"
            placeholder="Plazo en meses"
            required
            class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
          />
        </div>

        <div class="mb-4">
          <label for="interes" class="block text-sm font-medium text-gray-700"
            >Interés (%)</label
          >
          <input
            id="interes"
            v-model.number="newPrestamo.interes"
            type="number"
            placeholder="Tasa de interés"
            required
            class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
          />
        </div>

        <!-- Campo de Garantía -->
        <div class="mb-4">
          <label for="garantia" class="block text-sm font-medium text-gray-700"
            >Nombre de la Garantía</label
          >
          <input
            id="garantia"
            v-model="newPrestamo.garantia"
            type="text"
            placeholder="Nombre de la garantía"
            class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
          />
        </div>
      </div>
      <button
        type="submit"
        class="inline-flex items-center px-4 py-2 border border-transparent rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
      >
        Agregar Préstamo
      </button>
    </form>

    <!-- Tabla de préstamos -->
    <h1 class="text-2xl font-bold mb-4">Lista de Préstamos</h1>
    <table class="min-w-full bg-white rounded-md shadow-md">
      <thead>
        <tr class="bg-gray-100">
          <th class="py-2 px-4 border">Cliente</th>
          <th class="py-2 px-4 border">Fecha de Inicio</th>
          <th class="py-2 px-4 border">Capital Prestado</th>
          <th class="py-2 px-4 border">Plazo</th>
          <th class="py-2 px-4 border">Interés</th>
          <th class="py-2 px-4 border">Garantía</th>
          <th class="py-2 px-4 border">Detalles</th>
          <th class="py-2 px-4 border">Eliminar</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="prestamo in prestamos"
          :key="prestamo.id"
          class="text-center border-t"
        >
          <td class="py-2 px-4 border">
            {{ getClientName(prestamo.clienteId) }}
          </td>
          <td class="py-2 px-4 border">{{ prestamo.fechaInicio }}</td>
          <td class="py-2 px-4 border">{{ prestamo.monto }}</td>
          <td class="py-2 px-4 border">{{ prestamo.plazo }} meses</td>
          <td class="py-2 px-4 border">{{ prestamo.interes }}%</td>
          <td class="py-2 px-4 border">{{ prestamo.garantia }}</td>
          <td class="py-2 px-4 border">
            <router-link
              :to="`/detalleprestamo/${prestamo.id}`"
              class="text-blue-600 hover:underline"
            >
              Ver Detalles
            </router-link>
          </td>
          <td class="py-2 px-4 border">
            <button
              @click="confirmDelete(prestamo.id)"
              class="text-red-600 hover:underline"
            >
              Eliminar
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Modal para Confirmar Eliminación -->
    <div
      v-if="showModal"
      class="fixed inset-0 flex items-center justify-center bg-black bg-opacity-50"
    >
      <div class="bg-white p-6 rounded-md">
        <h2 class="text-xl mb-4">Confirma la Eliminación</h2>
        <p>Ingresa el PIN para confirmar la eliminación: {{ pin }}</p>
        <input
          type="text"
          v-model="inputPin"
          placeholder="PIN de 4 dígitos"
          maxlength="4"
          class="border border-gray-300 rounded-md p-2 mt-2"
        />
        <div class="mt-4">
          <button
            @click="deletePrestamo"
            class="bg-red-600 text-white px-4 py-2 rounded"
          >
            Confirmar
          </button>
          <button
            @click="cancelDelete"
            class="bg-gray-300 text-gray-700 px-4 py-2 rounded"
          >
            Cancelar
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      BASE_URL:
        'https://firestore.googleapis.com/v1/projects/gustavosuarez-2023/databases/(default)/documents',
      API_KEY: 'YvrRcWQ8Bdt5PNg7SCq1PfQZwVP1TGK0LSafI8ol',
      prestamos: [],
      clientes: [],
      newPrestamo: {
        clienteId: '',
        fechaInicio: '',
        monto: 0,
        plazo: 0,
        interes: 0,
        garantia: '',
      },
      showModal: false,
      pin: this.generatePin(),
      inputPin: '',
      prestamoToDelete: null,
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
          }));
        } else {
          this.clientes = [];
        }
      } catch (error) {
        console.error('Error fetching clientes:', error);
      }
    },
    async fetchPrestamos() {
      try {
        const response = await fetch(
          `${this.BASE_URL}/prestamos?key=${this.API_KEY}`
        );
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const data = await response.json();
        if (data.documents) {
          this.prestamos = data.documents.map((doc) => ({
            id: doc.name.split('/').pop(),
            clienteId: doc.fields.clienteId
              ? doc.fields.clienteId.stringValue
              : '',
            fechaInicio: doc.fields.fechaInicio
              ? doc.fields.fechaInicio.stringValue
              : 'No especificada',
            monto: doc.fields.monto
              ? parseInt(doc.fields.monto.integerValue)
              : 0,
            plazo: doc.fields.plazo
              ? parseInt(doc.fields.plazo.integerValue)
              : 0,
            interes: doc.fields.interes ? doc.fields.interes.doubleValue : 0.0,
            garantia: doc.fields.garantia
              ? doc.fields.garantia.stringValue
              : 'Sin garantía',
          }));
        } else {
          this.prestamos = [];
        }
      } catch (error) {
        console.error('Error fetching prestamos:', error);
      }
    },
    async addPrestamo() {
      const newDoc = {
        fields: {
          clienteId: { stringValue: this.newPrestamo.clienteId },
          fechaInicio: { stringValue: this.newPrestamo.fechaInicio },
          monto: { integerValue: this.newPrestamo.monto },
          plazo: { integerValue: this.newPrestamo.plazo },
          interes: { doubleValue: this.newPrestamo.interes },
          garantia: { stringValue: this.newPrestamo.garantia },
        },
      };

      try {
        const response = await fetch(
          `${this.BASE_URL}/prestamos?key=${this.API_KEY}`,
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

        this.prestamos.push({ ...this.newPrestamo, id: Date.now() });

        this.newPrestamo = {
          clienteId: '',
          fechaInicio: '',
          monto: 0,
          plazo: 0,
          interes: 0,
          garantia: '',
        };
      } catch (error) {
        console.error('Error adding prestamo:', error);
      }
    },
    getClientName(clienteId) {
      const cliente = this.clientes.find((c) => c.id === clienteId);
      return cliente ? cliente.nombre : 'Desconocido';
    },
    confirmDelete(prestamoId) {
      this.prestamoToDelete = prestamoId;
      this.showModal = true;
    },
    cancelDelete() {
      this.showModal = false;
      this.inputPin = '';
    },
    generatePin() {
      return Math.floor(1000 + Math.random() * 9000).toString(); // Genera un PIN de 4 dígitos
    },
    async deletePrestamo() {
      if (this.inputPin === this.pin) {
        try {
          const response = await fetch(
            `${this.BASE_URL}/prestamos/${this.prestamoToDelete}?key=${this.API_KEY}`,
            {
              method: 'DELETE',
            }
          );
          if (!response.ok) {
            throw new Error(`HTTP error! status: ${response.status}`);
          }

          // Eliminar el préstamo de la lista local
          this.prestamos = this.prestamos.filter(
            (prestamo) => prestamo.id !== this.prestamoToDelete
          );
          this.showModal = false;
          this.inputPin = '';
          this.pin = this.generatePin(); // Genera un nuevo PIN para futuras eliminaciones
        } catch (error) {
          console.error('Error deleting prestamo:', error);
        }
      } else {
        alert('PIN incorrecto. Inténtalo de nuevo.');
      }
    },
  },
  mounted() {
    this.fetchClientes();
    this.fetchPrestamos();
  },
};
</script>

<style scoped>
/* Puedes agregar estilos adicionales aquí si es necesario */
</style>
