<template>
  <div class="p-6 max-w-full mx-auto">
    <h1 class="text-2xl font-bold mb-4">Detalles del Préstamo</h1>

    <!-- Detalles del préstamo -->
    <div class="mb-6">
      <h2 class="text-xl font-semibold">Información del Préstamo</h2>
      <p><strong>Cliente:</strong> {{ clienteNombre }}</p>
      <p><strong>Fecha de Inicio:</strong> {{ prestamo.fechaInicio }}</p>
      <p><strong>Capital Prestado:</strong> {{ prestamo.monto }}</p>
      <p><strong>Plazo:</strong> {{ prestamo.plazo }} meses</p>
      <p><strong>Interés:</strong> {{ prestamo.interes }}%</p>
      <p><strong>Garantía:</strong> {{ prestamo.garantia }}</p>
      <p><strong>Pago Mensual:</strong> {{ calcularPagoMensual }}</p>
    </div>

    <!-- Formulario para agregar un pago -->
    <h2 class="text-xl font-semibold mb-2">Agregar Pago</h2>
    <form @submit.prevent="addPago" class="mb-6">
      <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
        <div class="mb-4">
          <label for="montoPago" class="block text-sm font-medium text-gray-700"
            >Monto del Pago</label
          >
          <input
            id="montoPago"
            v-model.number="nuevoPago.monto"
            type="number"
            placeholder="Monto del pago"
            required
            class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
          />
        </div>
        <div class="mb-4">
          <label for="fechaPago" class="block text-sm font-medium text-gray-700"
            >Fecha del Pago</label
          >
          <input
            id="fechaPago"
            v-model="nuevoPago.fecha"
            type="date"
            required
            class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
          />
        </div>
      </div>
      <button
        type="submit"
        class="inline-flex items-center px-4 py-2 border border-transparent rounded-md shadow-sm text-white bg-indigo-600 hover:bg-indigo-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-indigo-500"
      >
        Agregar Pago
      </button>
    </form>

    <!-- Tabla de Pagos Realizados -->
    <h2 class="text-xl font-semibold mb-2">Pagos Realizados</h2>
    <table class="min-w-full bg-white rounded-md shadow-md">
      <thead>
        <tr class="bg-gray-100">
          <th class="py-2 px-4 border">Fecha</th>
          <th class="py-2 px-4 border">Monto</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="pago in pagos" :key="pago.id" class="text-center border-t">
          <td class="py-2 px-4 border">{{ pago.fecha }}</td>
          <td class="py-2 px-4 border">{{ pago.monto }}</td>
        </tr>
      </tbody>
    </table>

    <!-- Vencimientos -->
    <h2 class="text-xl font-semibold mb-2">Vencimientos</h2>
    <table class="min-w-full bg-white rounded-md shadow-md">
      <thead>
        <tr class="bg-gray-100">
          <th class="py-2 px-4 border">Vencimiento</th>
          <th class="py-2 px-4 border">Monto Total</th>
          <th class="py-2 px-4 border">Monto Cobrado</th>
          <th class="py-2 px-4 border">Monto Pendiente</th>
          <th class="py-2 px-4 border">Estado</th>
          <th class="py-2 px-4 border">Acciones</th>
        </tr>
      </thead>
      <tbody>
        <tr
          v-for="vencimiento in vencimientos"
          :key="vencimiento.id"
          class="text-center border-t"
        >
          <td class="py-2 px-4 border">{{ vencimiento.fecha }}</td>
          <td class="py-2 px-4 border">{{ vencimiento.monto }}</td>
          <td class="py-2 px-4 border">
            {{ getMontoCobrado(vencimiento.id) }}
          </td>
          <td class="py-2 px-4 border">{{ getMontoPendiente(vencimiento) }}</td>
          <td class="py-2 px-4 border">
            {{ vencimiento.pagado ? 'Pagado' : 'Pendiente' }}
          </td>
          <td class="py-2 px-4 border">
            <button
              @click="openPaymentForm(vencimiento)"
              class="text-blue-600 hover:underline"
            >
              Agregar Pago
            </button>
          </td>
        </tr>
      </tbody>
    </table>

    <!-- Formulario para agregar pago a vencimiento seleccionado -->
    <div v-if="selectedVencimiento" class="mt-6">
      <h3 class="text-lg font-semibold">
        Agregar Pago al Vencimiento Seleccionado
      </h3>
      <p><strong>Fecha:</strong> {{ selectedVencimiento.fecha }}</p>
      <p><strong>Monto Original:</strong> {{ selectedVencimiento.monto }}</p>
      <form @submit.prevent="addVencimientoPago" class="mt-4">
        <div class="mb-4">
          <label
            for="pagoVencimiento"
            class="block text-sm font-medium text-gray-700"
            >Monto del Pago</label
          >
          <input
            id="pagoVencimiento"
            v-model.number="pagoVencimientoMonto"
            type="number"
            placeholder="Monto del pago"
            required
            class="mt-1 block w-full px-3 py-2 border border-gray-300 rounded-md shadow-sm focus:outline-none focus:ring-indigo-500 focus:border-indigo-500 sm:text-sm"
          />
        </div>
        <button
          type="submit"
          class="inline-flex items-center px-4 py-2 border border-transparent rounded-md shadow-sm text-white bg-green-600 hover:bg-green-700 focus:outline-none focus:ring-2 focus:ring-offset-2 focus:ring-green-500"
        >
          Agregar Pago a Vencimiento
        </button>
      </form>

      <!-- Lista de Pagos del Vencimiento Seleccionado -->
      <h4 class="text-md font-semibold mt-4">
        Pagos Realizados para este Vencimiento
      </h4>
      <ul class="mt-2">
        <li
          v-for="pago in getPagosForVencimiento(selectedVencimiento.id)"
          :key="pago.id"
        >
          Pago: {{ pago.monto }} - Fecha: {{ pago.fecha }}
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      prestamo: {},
      clienteNombre: '',
      pagos: [],
      nuevoPago: {
        monto: 0,
        fecha: '',
      },
      vencimientos: [],
      prestamoId: this.$route.params.prestamoId,
      selectedVencimiento: null,
      pagoVencimientoMonto: 0,
    };
  },
  methods: {
    async fetchPrestamo() {
      try {
        const response = await fetch(
          `https://firestore.googleapis.com/v1/projects/gustavosuarez-2023/databases/(default)/documents/prestamos/${this.prestamoId}`
        );
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const data = await response.json();
        if (data.fields) {
          this.prestamo = {
            id: this.prestamoId,
            clienteId: data.fields.clienteId?.stringValue || '',
            fechaInicio:
              data.fields.fechaInicio?.stringValue || 'No especificada',
            monto: data.fields.monto
              ? parseInt(data.fields.monto.integerValue)
              : 0,
            plazo: data.fields.plazo
              ? parseInt(data.fields.plazo.integerValue)
              : 0,
            interes: data.fields.interes
              ? data.fields.interes.doubleValue
              : 0.0,
            garantia: data.fields.garantia?.stringValue || 'Sin garantía',
          };
          await this.fetchCliente(this.prestamo.clienteId);
          await this.fetchPagos(); // Esperar a que se carguen los pagos
          await this.fetchVencimientos(); // Esperar a que se carguen los vencimientos
        }
      } catch (error) {
        console.error('Error fetching prestamo:', error);
      }
    },

    async fetchCliente(clienteId) {
      try {
        const response = await fetch(
          `https://firestore.googleapis.com/v1/projects/gustavosuarez-2023/databases/(default)/documents/clientes/${clienteId}`
        );
        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }
        const data = await response.json();
        this.clienteNombre = data.fields.nombre.stringValue || 'Sin nombre';
      } catch (error) {
        console.error('Error fetching cliente:', error);
      }
    },

    async fetchPagos() {
      const query = {
        structuredQuery: {
          from: [{ collectionId: 'pagos' }],
          where: {
            fieldFilter: {
              field: { fieldPath: 'prestamoId' },
              op: 'EQUAL',
              value: { stringValue: this.prestamoId },
            },
          },
          orderBy: [{ field: { fieldPath: 'fecha' }, direction: 'ASC' }],
        },
      };

      try {
        const response = await fetch(
          `https://firestore.googleapis.com/v1/projects/gustavosuarez-2023/databases/(default)/documents:runQuery`,
          {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json',
            },
            body: JSON.stringify(query),
          }
        );

        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        const data = await response.json();
        this.pagos = data.map((doc) => ({
          id: doc.document.name.split('/').pop(),
          monto: doc.document.fields.monto.integerValue,
          fecha: doc.document.fields.fecha.stringValue,
          vencimientoId:
            doc.document.fields.vencimientoId?.integerValue || null,
        }));
      } catch (error) {
        console.error('Error fetching pagos:', error);
      }
    },

    async fetchVencimientos() {
      const startDate = new Date(this.prestamo.fechaInicio);
      const monthlyInterestPayment =
        (this.prestamo.monto * this.prestamo.interes) / 100;

      for (let i = 1; i <= this.prestamo.plazo; i++) {
        const vencimientoDate = new Date(startDate);
        vencimientoDate.setMonth(startDate.getMonth() + i);
        this.vencimientos.push({
          id: vencimientoDate.getTime(),
          fecha: vencimientoDate.toISOString().split('T')[0],
          monto: this.prestamo.monto / this.prestamo.plazo,
          pagado: false,
        });
      }

      if (this.prestamo.plazo === 0) {
        const today = new Date();
        let vencimientoDate = new Date(startDate);
        while (vencimientoDate <= today) {
          this.vencimientos.push({
            id: vencimientoDate.getTime(),
            fecha: vencimientoDate.toISOString().split('T')[0],
            monto: monthlyInterestPayment,
            pagado: false,
          });
          vencimientoDate.setMonth(vencimientoDate.getMonth() + 1);
        }
      }
    },

    async addPago() {
      const newDoc = {
        fields: {
          prestamoId: { stringValue: this.prestamoId },
          monto: { integerValue: this.nuevoPago.monto },
          fecha: { stringValue: this.nuevoPago.fecha },
        },
      };

      try {
        const response = await fetch(
          `https://firestore.googleapis.com/v1/projects/gustavosuarez-2023/databases/(default)/documents/pagos`,
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

        console.log('Pago agregado correctamente:', newDoc); // Debug: Confirmar que se ha agregado

        // Actualizar la lista de pagos localmente
        this.pagos.push({
          id: Date.now(),
          monto: this.nuevoPago.monto,
          fecha: this.nuevoPago.fecha,
          vencimientoId: null, // Inicialmente sin vinculación
        });

        this.nuevoPago = { monto: 0, fecha: '' };
        await this.fetchPagos(); // Recargar pagos para asegurarse de que se muestren correctamente
      } catch (error) {
        console.error('Error adding pago:', error);
      }
    },

    openPaymentForm(vencimiento) {
      this.selectedVencimiento = vencimiento;
      this.pagoVencimientoMonto = 0; // Reiniciar el monto del pago
    },

    async addVencimientoPago() {
      if (!this.selectedVencimiento) return;

      const newDoc = {
        fields: {
          prestamoId: { stringValue: this.prestamoId },
          monto: { integerValue: this.pagoVencimientoMonto },
          fecha: { stringValue: new Date().toISOString().split('T')[0] },
          vencimientoId: { integerValue: this.selectedVencimiento.id },
        },
      };

      try {
        const response = await fetch(
          `https://firestore.googleapis.com/v1/projects/gustavosuarez-2023/databases/(default)/documents/pagos`,
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

        console.log('Pago de vencimiento agregado correctamente:', newDoc); // Debug: Confirmar que se ha agregado

        // Actualizar la lista de pagos localmente
        const nuevoPago = {
          id: Date.now(),
          monto: this.pagoVencimientoMonto,
          fecha: new Date().toISOString().split('T')[0],
          vencimientoId: this.selectedVencimiento.id,
        };

        this.pagos.push(nuevoPago);

        // Actualizar el estado del vencimiento
        this.selectedVencimiento.monto -= this.pagoVencimientoMonto;
        if (this.selectedVencimiento.monto <= 0) {
          this.selectedVencimiento.pagado = true;
        }

        this.pagoVencimientoMonto = 0;
        this.selectedVencimiento = null;
        await this.fetchPagos(); // Recargar pagos después de agregar uno
      } catch (error) {
        console.error('Error adding vencimiento pago:', error);
      }
    },

    getPagosForVencimiento(vencimientoId) {
      return this.pagos.filter((pago) => pago.vencimientoId === vencimientoId);
    },

    getMontoCobrado(vencimientoId) {
      return this.getPagosForVencimiento(vencimientoId).reduce(
        (total, pago) => total + pago.monto,
        0
      );
    },

    getMontoPendiente(vencimiento) {
      return vencimiento.monto - this.getMontoCobrado(vencimiento.id);
    },
  },
  mounted() {
    this.fetchPrestamo();
  },
};
</script>

<style scoped>
/* Puedes agregar estilos adicionales aquí si es necesario */
</style>
