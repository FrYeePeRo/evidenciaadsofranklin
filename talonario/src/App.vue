<template>
  <div id="app" class="container mt-4">
    <div class="text-center bg-primary text-white py-3 rounded">
      <h1 class="mb-0">TALONARIO DE RIFAS</h1>
    </div>

    <div class="row mt-4">
      <div class="col-md-6">
        <div class="card shadow-sm">
          <div class="card-body">
            <h5 class="text-primary">Información del Talonario</h5>
            <p><strong>Premio:</strong> $5000</p>
            <p><strong>Valor boleta:</strong> $100</p>
            <p><strong>Lotería:</strong> La Perla</p>
            <p><strong>Cantidad boletas:</strong> 100</p>
            <p><strong>Fecha sorteo:</strong> 2025-07-30</p>
          </div>
        </div>
      </div>

      <div class="col-md-6">
        <div class="card shadow-sm">
          <div class="card-body">
            <h5 class="text-primary">Resumen de estados</h5>
            <div class="mb-2">
              <button @click="setEstadoActivo('disponible')"
                :class="['btn me-2', currentStatus === 'disponible' ? 'btn-primary' : 'btn-outline-primary']">
                Disponibles: {{ disponibles }}
              </button>
              <button @click="setEstadoActivo('apartada')"
                :class="['btn me-2', currentStatus === 'apartada' ? 'btn-warning text-dark' : 'btn-outline-warning']">
                Apartadas: {{ apartadas }}
              </button>
              <button @click="setEstadoActivo('vendida')"
                :class="['btn', currentStatus === 'vendida' ? 'btn-success' : 'btn-outline-success']">
                Vendidas: {{ vendidas }}
              </button>
            </div>
            <div class="bg-light p-3 rounded border-start border-primary border-5">
              <p><strong>Dinero vendido:</strong> <span class="text-success">${{ totalVendido }}</span></p>
              <p><strong>Dinero por cobrar:</strong> <span class="text-danger">${{ totalPorCobrar }}</span></p>
            </div>
          </div>
        </div>
      </div>
    </div>

    <div class="mt-4">
      <h5>Boletas del Talonario</h5>
      <div class="mb-3 d-flex justify-content-center gap-2">
        <button class="btn btn-primary" @click="setAccionActual('disponible')"
          :class="{ active: accionActual === 'disponible' }">Disponible</button>
        <button class="btn btn-warning text-dark" @click="setAccionActual('apartada')"
          :class="{ active: accionActual === 'apartada' }">Apartada</button>
        <button class="btn btn-success" @click="setAccionActual('vendida')"
          :class="{ active: accionActual === 'vendida' }">Vendida</button>
      </div>

      <div class="d-flex flex-wrap gap-2">
        <div
          v-for="(boleta, index) in boletasFiltradas"
          :key="index"
          :class="['boleta', estadoClase(boleta.estado)]"
          @click="cambiarEstadoBoleta(index)">
          <div class="boleta-numero">{{ boleta.numero }}</div>
          <small class="fw-bold">{{ boleta.estado.toUpperCase() }}</small>
        </div>
      </div>
    </div>

    <div class="d-flex justify-content-center gap-2 my-4">
      <button class="btn text-white fw-bold" style="background-color: #28a745;">
        <i class="fa fa-print me-2"></i> IMPRIMIR BOLETAS
      </button>

      <button class="btn text-dark fw-bold" style="background-color: #17e4fa;">
        <i class="fa fa-file-excel-o me-2"></i> EXPORTAR A EXCEL
      </button>

      <button class="btn text-dark fw-bold" style="background-color: #ffc107;">
        <i class="fa fa-plus me-2"></i> NUEVO TALONARIO
      </button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      currentStatus: 'disponible',
      accionActual: '', // <-- se agregó para controlar qué estado asignar
      boletas: Array.from({ length: 100 }, (_, i) => ({
        numero: String(i + 1).padStart(3, '0'),
        estado: 'disponible',
      }))
    };
  },
  computed: {
    disponibles() {
      return this.boletas.filter(b => b.estado === 'disponible').length;
    },
    apartadas() {
      return this.boletas.filter(b => b.estado === 'apartada').length;
    },
    vendidas() {
      return this.boletas.filter(b => b.estado === 'vendida').length;
    },
    totalVendido() {
      return this.vendidas * 100;
    },
    totalPorCobrar() {
      return this.apartadas * 100;
    },
    boletasFiltradas() {
      return this.boletas.filter(b => b.estado === this.currentStatus);
    }
  },
  methods: {
    estadoClase(estado) {
      return {
        'disponible': 'boleta-disponible',
        'apartada': 'boleta-apartada',
        'vendida': 'boleta-vendida'
      }[estado];
    },
    setEstadoActivo(estado) {
      this.currentStatus = estado;
    },
    setAccionActual(estado) {
      this.accionActual = estado;
    },
    cambiarEstadoBoleta(index) {
      if (!this.accionActual) return;

      const numeroBoleta = this.boletasFiltradas[index].numero;
      const boletaRealIndex = this.boletas.findIndex(b => b.numero === numeroBoleta);

      if (boletaRealIndex !== -1) {
        this.boletas[boletaRealIndex].estado = this.accionActual;
      }
    }
  }
};
</script>

<style scoped>
.boleta {
  width: 65px;
  height: 65px;
  text-align: center;
  border-radius: 8px;
  font-size: 14px;
  padding-top: 5px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.2);
  cursor: pointer;
}

.boleta-disponible {
  background-color: #0d6efd;
  color: white;
}

.boleta-apartada {
  background-color: #ffc107;
  color: black;
}

.boleta-vendida {
  background-color: #198754;
  color: white;
}

.boleta-numero {
  font-weight: bold;
  font-size: 16px;
}
</style>
