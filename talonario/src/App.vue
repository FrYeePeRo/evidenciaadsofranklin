<template>
  <div id="app" class="container-fluid p-0">
    <!-- DASHBOARD -->
    <div v-if="vistaActual === 'dashboard'" class="container-fluid p-0">
      <!-- Header -->
      <div class="row mb-4">
        <div class="col-12">
          <div class="bg-primary text-white p-4 rounded text-center">
            <h1 class="display-5 mb-2">🎫 Gestor de Rifas</h1>
            <p class="mb-0">Panel de Control Simplificado</p>
          </div>
        </div>
      </div>

      <!-- Estadísticas -->
      <div class="row mb-4">
        <div class="col-md-4 mb-3">
          <div class="card bg-primary text-white">
            <div class="card-body text-center">
              <i class="fas fa-ticket-alt fa-2x mb-2"></i>
              <h4>{{ estadisticas.totalRifas }}</h4>
              <p class="mb-0">Total Rifas</p>
            </div>
          </div>
        </div>
        <div class="col-md-4 mb-3">
          <div class="card bg-success text-white">
            <div class="card-body text-center">
              <i class="fas fa-play-circle fa-2x mb-2"></i>
              <h4>{{ estadisticas.rifasActivas }}</h4>
              <p class="mb-0">Activas</p>
            </div>
          </div>
        </div>
        <div class="col-md-4 mb-3">
          <div class="card bg-warning text-dark">
            <div class="card-body text-center">
              <i class="fas fa-dollar-sign fa-2x mb-2"></i>
              <h4>${{ estadisticas.totalRecaudado.toLocaleString() }}</h4>
              <p class="mb-0">Recaudado</p>
            </div>
          </div>
        </div>
      </div>

      <!-- Controles -->
      <div class="row mb-">
        <div class="col-md-10">
          <h3 class="text-primary">Mis Rifas</h3>
        </div>
        <div class="col-md-2 text-end ">
          <button class="btn btn-success btn-lg" @click="cambiarVista('crear')">
            <i class="fas fa-plus me-2"></i>Nueva Rifa
          </button>
        </div>
      </div>

      <!-- Lista de Rifas -->
      <div v-if="rifas.length === 0" class="text-center py-5">
        <i class="fas fa-ticket-alt display-1 text-muted mb-3"></i>
        <h4 class="text-muted">No hay rifas creadas</h4>
        <button
          class="btn btn-primary btn-lg mt-3"
          @click="cambiarVista('crear')"
        >
          <i class="fas fa-plus me-2"></i>Crear Primera Rifa
        </button>
      </div>

      <div v-else class="row pt-3">
        <div
          v-for="rifa in rifas"
          :key="rifa.id"
          class="col-lg-4 col-md-6 mb-4"
        >
          <div
            class="card h-100 shadow-sm cursor-pointer"
            @click="seleccionarRifa(rifa)"
          >
            <div
              class="card-header d-flex justify-content-between align-items-center"
              :class="
                rifa.activa
                  ? 'bg-success text-white'
                  : 'bg-secondary text-white'
              "
            >
              <h6 class="mb-0 fw-bold">{{ rifa.nombre }}</h6>
              <span class="badge bg-light text-dark">{{
                rifa.activa ? "ACTIVA" : "INACTIVA"
              }}</span>
            </div>

            <div class="card-body">
              <div class="text-center mb-3">
                <h4 class="text-success">
                  ${{ rifa.premio.toLocaleString() }}
                </h4>
                <small class="text-muted">Premio Principal</small>
              </div>

              <div class="row text-center mb-3">
                <div class="col-4">
                  <strong>{{ rifa.cantidadBoletas }}</strong>
                  <br /><small class="text-muted">Boletas</small>
                </div>
                <div class="col-4">
                  <strong>${{ rifa.valorBoleta }}</strong>
                  <br /><small class="text-muted">Precio</small>
                </div>
                <div class="col-4">
                  <strong
                    >{{
                      Math.round((rifa.vendidas / rifa.cantidadBoletas) * 100)
                    }}%</strong
                  >
                  <br /><small class="text-muted">Vendido</small>
                </div>
              </div>

              <div class="progress mb-3">
                <div
                  class="progress-bar bg-success"
                  :style="{
                    width: (rifa.vendidas / rifa.cantidadBoletas) * 100 + '%',
                  }"
                ></div>
              </div>

              <ul class="list-unstyled small">
                <li>
                  <i class="fas fa-building text-primary me-1"></i>
                  {{ rifa.loteria }}
                </li>
                <li>
                  <i class="fas fa-calendar text-danger me-1"></i>
                  {{ rifa.fechaSorteo }}
                </li>
                <li>
                  <i class="fas fa-wallet text-success me-1"></i> ${{
                    rifa.recaudado.toLocaleString()
                  }}
                </li>
              </ul>
            </div>

            <div class="card-footer">
              <div class="d-flex justify-content-between align-items-center">
                <small class="text-muted">{{ rifa.fechaCreacion }}</small>
                <div>
                  <button
                    class="btn btn-sm btn-outline-primary me-1"
                    @click.stop="editarRifa(rifa)"
                  >
                    <i class="fas fa-edit"></i>
                  </button>
                  <button
                    class="btn btn-sm btn-outline-danger"
                    @click.stop="eliminarRifa(rifa.id)"
                  >
                    <i class="fas fa-trash"></i>
                  </button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- CREAR/EDITAR RIFA -->
    <div v-else-if="vistaActual === 'crear'" class="container py-0">
      <div class="row justify-content-center">
        <div class="col-md-12">
          <div class="card shadow">
            <div class="card-header bg-primary text-white">
              <div class="d-flex justify-content-between align-items-center">
                <h4 class="mb-0">
                  <i class="fas fa-plus-circle me-2"></i>
                  {{ rifaEditando ? "Editar Rifa" : "Nueva Rifa" }}
                </h4>
                <button
                  class="btn btn-outline-light btn-sm"
                  @click="regresarDashboard"
                >
                  <i class="fas fa-arrow-left me-1"></i>Regresar
                </button>
              </div>
            </div>

            <div class="card-body p-4">
              <form @submit.prevent="guardarRifa" novalidate>
                <!-- Nombre -->
                <div class="mb-3">
                  <label class="form-label fw-bold">
                    <i class="fas fa-tag me-1 text-primary"></i>Nombre de la
                    Rifa
                  </label>
                  <input
                    v-model="formularioRifa.nombre"
                    type="text"
                    class="form-control"
                    :class="{ 'is-invalid': errores.nombre }"
                    required
                    placeholder="Ej: Rifa Navideña 2025"
                  />
                  <div v-if="errores.nombre" class="invalid-feedback">
                    {{ errores.nombre }}
                  </div>
                </div>

                <!-- Premio y Valor -->
                <div class="row">
                  <div class="col-md-6 mb-3">
                    <label class="form-label fw-bold">
                      <i class="fas fa-gift me-1 text-warning"></i>Premio ($)
                    </label>
                    <input
                      v-model.number="formularioRifa.premio"
                      type="text"
                      min="1"
                      class="form-control"
                      :class="{ 'is-invalid': errores.premio }"
                      required
                      placeholder="ingrese su premio"
                    />
                    <div v-if="errores.premio" class="invalid-feedback">
                      {{ errores.premio }}
                    </div>
                  </div>
                  <div class="col-md-6 mb-3">
                    <label class="form-label fw-bold">
                      <i class="fas fa-dollar-sign me-1 text-success"></i>Valor
                      Boleta ($)
                    </label>
                    <input
                      v-model.number="formularioRifa.valorBoleta"
                      type="number"
                      min="1"
                      class="form-control"
                      :class="{ 'is-invalid': errores.valorBoleta }"
                      required
                      placeholder="ingrese el valor de la boleta"
                    />
                    <div v-if="errores.valorBoleta" class="invalid-feedback">
                      {{ errores.valorBoleta }}
                    </div>
                  </div>
                </div>

                <!-- Cantidad y Lotería -->
                <div class="row">
                  <div class="col-md-6 mb-3">
                    <label class="form-label fw-bold">
                      <i class="fas fa-ticket-alt me-1 text-info"></i>Cantidad
                      Boletas
                    </label>
                    <select
                      v-model.number="formularioRifa.cantidadBoletas"
                      class="form-select"
                      required
                    >
                      <option value="50">50 Boletas</option>
                      <option value="100">100 Boletas</option>
                      <option value="200">200 Boletas</option>
                      <option value="500">500 Boletas</option>
                    </select>
                  </div>
                  <div class="col-md-6 mb-3">
                    <label class="form-label fw-bold">
                      <i class="fas fa-ticket-alt me-1 text-info"></i>Lotería
                    </label>
                    <select
                      v-model.number="formularioRifa.loteria"
                      class="form-select"
                      required
                    >
                      <option value="" disabled selected>
                        Seleccione una Lotería
                      </option>
                      <option value="Lotería de Bogotá">
                        Lotería de Bogotá
                      </option>
                      <option value="Lotería de Medellín">
                        Lotería de Medellín
                      </option>
                      <option value="Lotería del Valle">
                        Lotería del Valle
                      </option>
                      <option value="Lotería de Cundinamarca">
                        Lotería de Cundinamarca
                      </option>
                      <option value="Lotería del Cauca">
                        Lotería del Cauca
                      </option>
                      <option value="Lotería de Boyacá">
                        Lotería de Boyacá
                      </option>
                      <option value="Lotería del Meta">Lotería del Meta</option>
                      <option value="Lotería de Manizales">
                        Lotería de Manizales
                      </option>
                      <option value="Lotería del Risaralda">
                        Lotería del Risaralda
                      </option>
                    </select>
                    <div v-if="errores.loteria" class="invalid-feedback">
                      {{ errores.loteria }}
                    </div>
                  </div>
                </div>

                <!--Fecha-->
                <div class="mb-3">
                  <label class="form-label fw-bold">
                    <i class="fas fa-calendar me-1 text-danger"></i>
                    Fecha del Sorteo *
                  </label>

                  <div class="input-group">
                    <input
                      type="date"
                      v-model="formularioRifa.fechaSorteo"
                      :min="fechaMinima"
                      class="form-control"
                      :class="{ 'is-invalid': errores.fechaSorteo }"
                      required
                    />
                    <span class="input-group-text">
                      <i class="fas fa-calendar-alt"></i>
                    </span>
                  </div>

                  <div v-if="errores.fechaSorteo" class="invalid-feedback">
                    {{ errores.fechaSorteo }}
                  </div>
                </div>

                <!-- Descripción -->
                <div class="mb-4">
                  <label class="form-label fw-bold">
                    <i class="fas fa-info-circle me-1 text-info"></i>Descripción
                    (Opcional)
                  </label>
                  <textarea
                    v-model="formularioRifa.descripcion"
                    class="form-control"
                    rows="3"
                    placeholder="Descripción de la rifa..."
                  ></textarea>
                </div>

                <!-- Preview -->
                <div
                  v-if="
                    formularioRifa.premio &&
                    formularioRifa.valorBoleta &&
                    formularioRifa.cantidadBoletas
                  "
                  class="alert alert-info"
                >
                  <h6 class="fw-bold mb-2">
                    <i class="fas fa-calculator me-1"></i>Resumen Financiero:
                  </h6>
                  <div class="row">
                    <div class="col-md-4">
                      <strong>Total Potencial:</strong><br />
                      ${{
                        (
                          formularioRifa.cantidadBoletas *
                          formularioRifa.valorBoleta
                        ).toLocaleString()
                      }}
                    </div>
                    <div class="col-md-4">
                      <strong>Premio:</strong><br />
                      ${{ formularioRifa.premio.toLocaleString() }}
                    </div>
                    <div class="col-md-4">
                      <strong>Ganancia:</strong><br />
                      ${{
                        (
                          formularioRifa.cantidadBoletas *
                            formularioRifa.valorBoleta -
                          formularioRifa.premio
                        ).toLocaleString()
                      }}
                    </div>
                  </div>
                </div>

                <!-- Botones -->
                <div class="d-flex justify-content-between">
                  <button
                    type="button"
                    class="btn btn-outline-secondary"
                    @click="limpiarFormulario"
                  >
                    <i class="fas fa-broom me-1"></i>Limpiar
                  </button>
                  <div class="d-flex gap-2">
                    <button
                      type="button"
                      class="btn btn-secondary"
                      @click="regresarDashboard"
                    >
                      <i class="fas fa-times me-1"></i>Cancelar
                    </button>
                    <button
                      type="submit"
                      class="btn btn-success"
                      :disabled="cargando"
                    >
                      <span
                        v-if="cargando"
                        class="spinner-border spinner-border-sm me-1"
                      ></span>
                      <i v-else class="fas fa-save me-1"></i>
                      {{ rifaEditando ? "Actualizar" : "Crear" }}
                    </button>
                  </div>
                </div>
              </form>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- TALONARIO -->
    <div v-else-if="vistaActual === 'talonario'" class="container-fluid py-4">
      <!-- Header con navegación -->
      <div class="d-flex justify-content-between align-items-center mb-4">
        <div>
          <nav class="mb-2">
            <ol class="breadcrumb mb-0">
              <li class="breadcrumb-item">
                <a
                  href="#"
                  @click.prevent="regresarDashboard"
                  class="text-decoration-none"
                >
                  <i class="fas fa-home me-1"></i>Dashboard
                </a>
              </li>
              <li class="breadcrumb-item active">
                {{ rifaSeleccionada.nombre }}
              </li>
            </ol>
          </nav>
          <h2 class="text-primary mb-0">🎫 {{ rifaSeleccionada.nombre }}</h2>
        </div>
        <button class="btn btn-outline-primary" @click="regresarDashboard">
          <i class="fas fa-arrow-left me-2"></i>Regresar
        </button>
      </div>

      <!-- Info y Estadísticas -->
      <div class="row mb-4">
        <div class="col-md-6">
          <div class="card">
            <div class="card-body">
              <h5 class="text-primary mb-3">
                <i class="fas fa-info-circle me-2"></i>Información
              </h5>
              <div class="row">
                <div class="col-6">
                  <ul class="list-unstyled">
                    <li>
                      <strong>Premio:</strong> ${{
                        rifaSeleccionada.premio.toLocaleString()
                      }}
                    </li>
                    <li>
                      <strong>Valor:</strong> ${{
                        rifaSeleccionada.valorBoleta
                      }}
                    </li>
                    <li>
                      <strong>Boletas:</strong>
                      {{ rifaSeleccionada.cantidadBoletas }}
                    </li>
                  </ul>
                </div>
                <div class="col-6">
                  <ul class="list-unstyled">
                    <li>
                      <strong>Lotería:</strong> {{ rifaSeleccionada.loteria }}
                    </li>
                    <li>
                      <strong>Sorteo:</strong>
                      {{ rifaSeleccionada.fechaSorteo }}
                    </li>
                    <li>
                      <strong>Estado:</strong>
                      <span class="badge bg-success">ACTIVA</span>
                    </li>
                  </ul>
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="col-md-6">
          <div class="card">
            <div class="card-body">
              <h5 class="text-primary mb-3">
                <i class="fas fa-chart-bar me-2"></i>Estadísticas
              </h5>
              <div class="row text-center mb-3">
                <div class="col-4">
                  <div class="bg-primary text-white p-2 rounded">
                    <strong>{{ estadosBoletas.disponibles }}</strong>
                    <br /><small>Disponibles</small>
                  </div>
                </div>
                <div class="col-4">
                  <div class="bg-warning text-dark p-2 rounded">
                    <strong>{{ estadosBoletas.apartadas }}</strong>
                    <br /><small>Apartadas</small>
                  </div>
                </div>
                <div class="col-4">
                  <div class="bg-success text-white p-2 rounded">
                    <strong>{{ estadosBoletas.vendidas }}</strong>
                    <br /><small>Vendidas</small>
                  </div>
                </div>
              </div>
              <div class="row text-center">
                <div class="col-4">
                  <strong class="text-success"
                    >${{ estadosBoletas.vendido.toLocaleString() }}</strong
                  >
                  <br /><small>Vendido</small>
                </div>
                <div class="col-4">
                  <strong class="text-warning"
                    >${{ estadosBoletas.apartado.toLocaleString() }}</strong
                  >
                  <br /><small>Por Cobrar</small>
                </div>
                <div class="col-4">
                  <strong class="text-info"
                    >${{ estadosBoletas.potencial.toLocaleString() }}</strong
                  >
                  <br /><small>Potencial</small>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Controles -->
      <div class="row mb-4">
        <div class="col-md-4">
          <div class="input-group">
            <span class="input-group-text">
              <i class="fas fa-search"></i>
            </span>
            <input
              v-model="busqueda"
              type="text"
              class="form-control"
              placeholder="Buscar número..."
            />
          </div>
        </div>
        <div class="col-md-2">
          <button
            class="btn btn-outline-secondary w-100"
            @click="limpiarFiltros"
          >
            <i class="fas fa-broom me-1"></i>Limpiar
          </button>
        </div>
        <div class="col-md-3">
          <select v-model="filtroEstado" class="form-select">
            <option value="">Todos los estados</option>
            <option value="disponible">Disponibles</option>
            <option value="apartada">Apartadas</option>
            <option value="vendida">Vendidas</option>
          </select>
        </div>
        <div class="col-md-3">
          <button class="btn btn-success w-100" @click="exportarPDF">
            <i class="fas fa-file-pdf me-1"></i>Exportar PDF
          </button>
        </div>
      </div>

      <!-- Tabla de Boletas en Filas de 10 -->
      <div class="card">
        <div class="card-body">
          <h5 class="card-title mb-4">
            <i class="fas fa-ticket-alt me-2"></i>
            Boletas del Talonario
            <span class="badge bg-secondary ms-2"
              >{{ boletasFiltradas.length }} boletas</span
            >
          </h5>

          <!-- Tabla de números -->
          <div class="table-responsive">
            <table class="table table-sm">
              <tbody>
                <tr v-for="fila in filasDeNumeros" :key="fila.indice">
                  <td
                    v-for="boleta in fila.boletas"
                    :key="boleta?.numero || 'empty'"
                    class="p-1"
                    style="width: 10%"
                  >
                    <div
                      v-if="boleta"
                      class="numero-boleta text-center p-2 rounded cursor-pointer"
                      :class="claseEstadoBoleta(boleta.estado)"
                      @click="abrirModal(boleta)"
                      :title="`Boleta #${
                        boleta.numero
                      } - ${boleta.estado.toUpperCase()}${
                        boleta.comprador ? ' - ' + boleta.comprador : ''
                      }`"
                    >
                      <strong>{{ boleta.numero }}</strong>
                      <br />
                      <small class="estado-text">{{
                        boleta.estado.charAt(0).toUpperCase()
                      }}</small>
                    </div>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>

          <!-- Paginación si hay muchas boletas -->
          <nav v-if="totalPaginas > 1" class="mt-4">
            <ul class="pagination justify-content-center">
              <li class="page-item" :class="{ disabled: paginaActual === 1 }">
                <button
                  class="page-link"
                  @click="cambiarPagina(paginaActual - 1)"
                >
                  <i class="fas fa-chevron-left"></i>
                </button>
              </li>
              <li
                v-for="pagina in totalPaginas"
                :key="pagina"
                class="page-item"
                :class="{ active: pagina === paginaActual }"
              >
                <button class="page-link" @click="cambiarPagina(pagina)">
                  {{ pagina }}
                </button>
              </li>
              <li
                class="page-item"
                :class="{ disabled: paginaActual === totalPaginas }"
              >
                <button
                  class="page-link"
                  @click="cambiarPagina(paginaActual + 1)"
                >
                  <i class="fas fa-chevron-right"></i>
                </button>
              </li>
            </ul>
          </nav>
        </div>
      </div>
    </div>

    <!-- MODAL BOLETA -->
    <div v-if="mostrarModal" class="modal d-block" tabindex="-1">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header bg-primary text-white">
            <h5 class="modal-title">
              <i class="fas fa-ticket-alt me-2"></i>Boleta #{{
                boletaSeleccionada?.numero
              }}
            </h5>
            <button
              type="button"
              class="btn-close btn-close-white"
              @click="cerrarModal"
            ></button>
          </div>
          <div class="modal-body">
            <form @submit.prevent="guardarBoleta">
              <!-- Estado -->
              <div class="mb-3">
                <label class="form-label fw-bold">Estado de la Boleta</label>
                <select
                  v-model="boletaSeleccionada.estado"
                  class="form-select"
                  required
                >
                  <option value="disponible">🔵 Disponible</option>
                  <option value="apartada">🟡 Apartada</option>
                  <option value="vendida">🟢 Vendida</option>
                </select>
              </div>

              <!-- Datos del comprador -->
              <div
                v-if="boletaSeleccionada.estado !== 'disponible'"
                class="border p-3 rounded bg-light"
              >
                <h6 class="text-primary mb-3">
                  <i class="fas fa-user me-2"></i>Datos del Comprador
                </h6>

                <div class="mb-3">
                  <label class="form-label">Nombre Completo *</label>
                  <input
                    v-model="boletaSeleccionada.comprador"
                    type="text"
                    class="form-control"
                    required
                    placeholder="Nombre del comprador"
                  />
                </div>

                <div class="row">
                  <div class="col-md-6 mb-3">
                    <label class="form-label">Teléfono</label>
                    <input
                      v-model="boletaSeleccionada.telefono"
                      type="tel"
                      class="form-control"
                      required
                      pattern="^\+57 \d{3} \d{3} \d{4}$"
                      placeholder="+57 300 123 4567"
                    />
                  </div>
                  <div class="col-md-6 mb-3">
                    <label class="form-label">Cédula</label>
                    <input
                      v-model="boletaSeleccionada.cedula"
                      type="text"
                      class="form-control"
                      required
                      pattern="^\d{6,10}$"
                      placeholder="123456789"
                    />
                  </div>
                </div>

                <div class="mb-3">
                  <label class="form-label">Observaciones</label>
                  <textarea
                    v-model="boletaSeleccionada.observaciones"
                    class="form-control"
                    rows="2"
                    placeholder="Notas adicionales..."
                  ></textarea>
                </div>
              </div>
            </form>
          </div>
          <div class="modal-footer">
            <button
              type="button"
              class="btn btn-secondary"
              @click="cerrarModal"
            >
              <i class="fas fa-times me-1"></i>Cancelar
            </button>
            <button
              type="button"
              class="btn btn-primary"
              @click="guardarBoleta"
            >
              <i class="fas fa-save me-1"></i>Guardar
            </button>
          </div>
        </div>
      </div>
    </div>
    <div v-if="mostrarModal" class="modal-backdrop fade show"></div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onBeforeUnmount, watch } from "vue";
import Swal from "sweetalert2";

// Reactive data
const vistaActual = ref("dashboard");
const cargando = ref(false);

// Rifas
const rifas = ref([]);
const rifaSeleccionada = ref(null);
const rifaEditando = ref(null);

// Formulario
const formularioRifa = ref({
  nombre: "",
  premio: null,
  valorBoleta: null,
  cantidadBoletas: 100,
  loteria: "",
  fechaSorteo: "",
  descripcion: "",
});
const errores = ref({});

// Boletas
const boletas = ref([]);
const filtroEstado = ref("");
const busqueda = ref("");
const paginaActual = ref(1);
const boletasPorPagina = ref(100); // Mostrar más boletas por página

// Modal
const mostrarModal = ref(false);
const boletaSeleccionada = ref(null);

// Computed properties
const estadisticas = computed(() => ({
  totalRifas: rifas.value.length,
  rifasActivas: rifas.value.filter((r) => r.activa).length,
  totalRecaudado: rifas.value.reduce(
    (total, rifa) => total + rifa.recaudado,
    0
  ),
  promedioVentas:
    rifas.value.length > 0
      ? rifas.value.reduce(
          (avg, rifa) => avg + (rifa.vendidas / rifa.cantidadBoletas) * 100,
          0
        ) / rifas.value.length
      : 0,
}));

const fechaMinima = computed(() => {
  return new Date().toISOString().split("T")[0];
});

const estadosBoletas = computed(() => {
  if (!boletas.value.length)
    return {
      disponibles: 0,
      apartadas: 0,
      vendidas: 0,
      vendido: 0,
      apartado: 0,
      potencial: 0,
    };

  const disponibles = boletas.value.filter(
    (b) => b.estado === "disponible"
  ).length;
  const apartadas = boletas.value.filter((b) => b.estado === "apartada").length;
  const vendidas = boletas.value.filter((b) => b.estado === "vendida").length;

  return {
    disponibles,
    apartadas,
    vendidas,
    vendido: vendidas * rifaSeleccionada.value.valorBoleta,
    apartado: apartadas * rifaSeleccionada.value.valorBoleta,
    potencial:
      rifaSeleccionada.value.cantidadBoletas *
      rifaSeleccionada.value.valorBoleta,
  };
});

const boletasFiltradas = computed(() => {
  let filtradas = boletas.value;

  if (filtroEstado.value) {
    filtradas = filtradas.filter((b) => b.estado === filtroEstado.value);
  }

  if (busqueda.value) {
    filtradas = filtradas.filter((b) => b.numero.includes(busqueda.value));
  }

  return filtradas;
});

const totalPaginas = computed(() => {
  return Math.ceil(boletasFiltradas.value.length / boletasPorPagina.value);
});

// Computed para organizar boletas en filas de 10
const filasDeNumeros = computed(() => {
  const boletasPag = boletasFiltradas.value.slice(
    (paginaActual.value - 1) * boletasPorPagina.value,
    paginaActual.value * boletasPorPagina.value
  );

  const filas = [];
  for (let i = 0; i < boletasPag.length; i += 10) {
    const fila = {
      indice: Math.floor(i / 10),
      boletas: [],
    };

    // Llenar la fila con hasta 10 boletas
    for (let j = 0; j < 10; j++) {
      if (boletasPag[i + j]) {
        fila.boletas.push(boletasPag[i + j]);
      } else {
        fila.boletas.push(null); // Espacio vacío
      }
    }

    filas.push(fila);
  }

  return filas;
});

// Lifecycle hooks
onMounted(() => {
  console.log("🚀 Componente montado");
  cargarRifas();
  configurarEventos();
  mostrarBienvenida();
});

onBeforeUnmount(() => {
  console.log("🔄 Antes de desmontar");
  limpiarEventos();
});

// Methods
const cambiarVista = (vista) => {
  vistaActual.value = vista;
  limpiarFormulario();
};

const regresarDashboard = () => {
  rifaEditando.value = null;
  cambiarVista("dashboard");
};

const cargarRifas = () => {
  const rifasGuardadas = localStorage.getItem("rifas");
  if (rifasGuardadas) {
    rifas.value = JSON.parse(rifasGuardadas);
  } else {
    // Datos de ejemplo
    rifas.value = [
      {
        id: 1,
        nombre: "Rifa Navideña 2025",
        premio: 5000,
        valorBoleta: 100,
        cantidadBoletas: 100,
        loteria: "La Perla",
        fechaSorteo: "2025-12-24",
        fechaCreacion: "15/01/2025",
        descripcion: "Rifa especial de Navidad",
        activa: true,
        vendidas: 35,
        recaudado: 3500,
      },
    ];
    guardarRifas();
  }
};

const guardarRifas = () => {
  localStorage.setItem("rifas", JSON.stringify(rifas.value));
};

const validarFormulario = () => {
  errores.value = {};

  if (!formularioRifa.value.nombre.trim()) {
    errores.value.nombre = "El nombre es obligatorio";
  }

  if (!formularioRifa.value.premio || formularioRifa.value.premio <= 0) {
    errores.value.premio = "El premio debe ser mayor a 0";
  }

  if (
    !formularioRifa.value.valorBoleta ||
    formularioRifa.value.valorBoleta <= 0
  ) {
    errores.value.valorBoleta = "El valor debe ser mayor a 0";
  }

  if (!formularioRifa.value.loteria.trim()) {
    errores.value.loteria = "La lotería es obligatoria";
  }

  if (!formularioRifa.value.fechaSorteo) {
    errores.value.fechaSorteo = "La fecha es obligatoria";
  }

  return Object.keys(errores.value).length === 0;
};

const guardarRifa = async () => {
  if (!validarFormulario()) return;

  cargando.value = true;

  try {
    await new Promise((resolve) => setTimeout(resolve, 1000));

    if (rifaEditando.value) {
      // Encuentra el índice de la rifa que se está editando
      const index = rifas.value.findIndex((r) => r.id === rifaEditando.value.id);
      
      // Actualiza la rifa existente con los nuevos datos
      rifas.value[index] = {
        ...rifas.value[index], // Mantén los datos existentes (como id, fechaCreacion, etc.)
        ...formularioRifa.value, // Sobrescribe con los nuevos datos del formulario
        fechaModificacion: new Date().toLocaleDateString(),
      };

      Swal.fire({
        title: "¡Actualizada!",
        text: "La rifa se actualizó correctamente",
        icon: "success",
        timer: 2000,
        showConfirmButton: false,
      });
    } else {
      // Crea una nueva rifa si no se está editando
      const nuevaRifa = {
        ...formularioRifa.value,
        id: Date.now(),
        fechaCreacion: new Date().toLocaleDateString(),
        activa: true,
        vendidas: 0,
        recaudado: 0,
      };

      rifas.value.push(nuevaRifa);

      Swal.fire({
        title: "¡Creada!",
        text: "La rifa se creó correctamente",
        icon: "success",
        timer: 2000,
        showConfirmButton: false,
      });
    }

    guardarRifas();
    cambiarVista("dashboard");
    rifaEditando.value = null; // Limpia el estado de edición
  } catch (error) {
    Swal.fire({
      title: "Error",
      text: "Hubo un problema al guardar",
      icon: "error",
    });
  } finally {
    cargando.value = false;
  }
};

const editarRifa = (rifa) => {
  rifaEditando.value = rifa;
  formularioRifa.value = {
    nombre: rifa.nombre,
    premio: rifa.premio,
    valorBoleta: rifa.valorBoleta,
    cantidadBoletas: rifa.cantidadBoletas,
    loteria: rifa.loteria,
    fechaSorteo: rifa.fechaSorteo,
    descripcion: rifa.descripcion || "",
  };
  cambiarVista("crear");
};


const eliminarRifa = async (rifaId) => {
  const result = await Swal.fire({
    title: "¿Eliminar rifa?",
    text: "Esta acción no se puede deshacer",
    icon: "warning",
    showCancelButton: true,
    confirmButtonText: "Sí, eliminar",
    cancelButtonText: "Cancelar",
  });

  if (result.isConfirmed) {
    rifas.value = rifas.value.filter((r) => r.id !== rifaId);
    guardarRifas();

    Swal.fire({
      title: "¡Eliminada!",
      text: "La rifa fue eliminada",
      icon: "success",
      timer: 2000,
      showConfirmButton: false,
    });
  }
};

const seleccionarRifa = (rifa) => {
  rifaSeleccionada.value = rifa;
  inicializarBoletas();
  cambiarVista("talonario");
};

const inicializarBoletas = () => {
  const clave = `boletas_${rifaSeleccionada.value.id}`;
  const boletasGuardadas = localStorage.getItem(clave);

  if (boletasGuardadas) {
    boletas.value = JSON.parse(boletasGuardadas);
  } else {
    boletas.value = Array.from(
      { length: rifaSeleccionada.value.cantidadBoletas },
      (_, i) => ({
        numero: String(i + 1).padStart(3, "0"),
        estado: "disponible",
        comprador: "",
        telefono: "",
        cedula: "",
        observaciones: "",
        fecha: null,
      })
    );
    guardarBoletas();
  }

  filtroEstado.value = "";
  paginaActual.value = 1;
};

const guardarBoletas = () => {
  const clave = `boletas_${rifaSeleccionada.value.id}`;
  localStorage.setItem(clave, JSON.stringify(boletas.value));

  // Actualizar estadísticas
  const vendidas = boletas.value.filter((b) => b.estado === "vendida").length;
  const index = rifas.value.findIndex(
    (r) => r.id === rifaSeleccionada.value.id
  );

  if (index !== -1) {
    rifas.value[index].vendidas = vendidas;
    rifas.value[index].recaudado =
      vendidas * rifaSeleccionada.value.valorBoleta;
    rifaSeleccionada.value.vendidas = vendidas;
    rifaSeleccionada.value.recaudado =
      vendidas * rifaSeleccionada.value.valorBoleta;
    guardarRifas();
  }
};

const abrirModal = (boleta) => {
  boletaSeleccionada.value = {
    numero: boleta.numero,
    estado: boleta.estado,
    comprador: boleta.comprador || "",
    telefono: boleta.telefono || "",
    cedula: boleta.cedula || "",
    observaciones: boleta.observaciones || "",
    fecha: boleta.fecha || null
  };
  mostrarModal.value = true;
};

const cerrarModal = () => {
  mostrarModal.value = false;
  boletaSeleccionada.value = null;
};

const guardarBoleta = async () => {
  // Validar campos requeridos para boletas vendidas/apartadas
  if (boletaSeleccionada.value.estado !== "disponible") {
    const camposRequeridos = [
      { campo: "comprador", nombre: "Nombre del comprador" },
      { campo: "telefono", nombre: "Teléfono" },
      { campo: "cedula", nombre: "Cédula" },
    ];

    const camposFaltantes = camposRequeridos.filter(
      ({ campo }) => !boletaSeleccionada.value[campo]?.trim()
    );

    if (camposFaltantes.length > 0) {
      const mensaje = `Los siguientes campos son obligatorios:\n${camposFaltantes
        .map(({ nombre }) => `• ${nombre}`)
        .join("\n")}`;

      Swal.fire({
        title: "Faltan datos",
        text: mensaje,
        icon: "warning",
        confirmButtonText: "Entendido",
      });
      return;
    }

    // Validar formato de teléfono (opcional)
    if (!/^[\d\s+-]{7,15}$/.test(boletaSeleccionada.value.telefono)) {
      Swal.fire({
        title: "Teléfono inválido",
        text: "Por favor ingrese un número de teléfono válido",
        icon: "warning",
        confirmButtonText: "Entendido",
      });
      return;
    }

    // Validar formato de cédula (opcional)
    if (!/^\d{6,12}$/.test(boletaSeleccionada.value.cedula)) {
      Swal.fire({
        title: "Cédula inválida",
        text: "Por favor ingrese una cédula válida (solo números)",
        icon: "warning",
        confirmButtonText: "Entendido",
      });
      return;
    }
  }

 // Encuentra el índice de la boleta en el array
  const index = boletas.value.findIndex(
    b => b.numero === boletaSeleccionada.value.numero
  );

  if (index !== -1) {
    // Actualiza SOLO los campos editados, manteniendo los demás
    boletas.value[index] = {
      ...boletas.value[index], // Mantiene los datos existentes
      ...boletaSeleccionada.value, // Sobrescribe con los nuevos valores
      fecha: boletaSeleccionada.value.fecha || new Date().toLocaleDateString() // Actualiza fecha si es necesario
    };
    
    guardarBoletas();
    cerrarModal();
    
    Swal.fire({
      title: "¡Actualizada!",
      text: `Boleta #${boletaSeleccionada.value.numero} actualizada`,
      icon: "success",
      timer: 1500,
      showConfirmButton: false
    });
  }
};
const claseEstadoBoleta = (estado) => {
  const clases = {
    disponible: "bg-primary text-white border-primary",
    apartada: "bg-warning text-dark border-warning",
    vendida: "bg-success text-white border-success",
  };
  return clases[estado] || "";
};

const cambiarPagina = (pagina) => {
  if (pagina >= 1 && pagina <= totalPaginas.value) {
    paginaActual.value = pagina;
  }
};

const limpiarFiltros = () => {
  filtroEstado.value = "";
  busqueda.value = "";
  paginaActual.value = 1;

  Swal.fire({
    title: "Filtros limpiados",
    icon: "info",
    timer: 1000,
    showConfirmButton: false,
  });
};

const limpiarFormulario = () => {
  formularioRifa.value = {
    nombre: "",
    premio: null,
    valorBoleta: null,
    cantidadBoletas: 100,
    loteria: "",
    fechaSorteo: "",
    descripcion: "",
  };
  errores.value = {};
  rifaEditando.value = null;
};

const configurarEventos = () => {
  const manejarEscape = (e) => {
    if (e.key === "Escape" && mostrarModal.value) {
      cerrarModal();
    }
  };
  document.addEventListener("keydown", manejarEscape);

  // Guardar referencia para poder removerlo
  configurarEventos.manejarEscape = manejarEscape;
};

const limpiarEventos = () => {
  if (configurarEventos.manejarEscape) {
    document.removeEventListener("keydown", configurarEventos.manejarEscape);
  }
};

const mostrarBienvenida = () => {
  Swal.fire({
    title: "¡Bienvenido!",
    text: "Gestor de Rifas",
    icon: "success",
    timer: 2000,
    showConfirmButton: false,
  });
};

const exportarPDF = async () => {
  try {
    Swal.fire({
      title: "Generando PDF...",
      allowOutsideClick: false,
      didOpen: () => {
        Swal.showLoading();
      },
    });

    // Importar dinámicamente la librería
    const html2pdf = (await import("html2pdf.js")).default;

    // Crear un contenedor temporal para el PDF
    const pdfContainer = document.createElement("div");
    pdfContainer.style.width = "210mm";
    pdfContainer.style.padding = "20px";
    pdfContainer.style.backgroundColor = "white";

    // 1. Clonar la sección de información
    const infoSection = document
      .querySelector(".card:first-child")
      .cloneNode(true);
    pdfContainer.appendChild(infoSection);

    // 2. Añadir título para los números vendidos/apartados
    const title = document.createElement("h3");
    title.textContent = "Boletas Vendidas/Apartadas";
    title.style.marginTop = "20px";
    title.style.marginBottom = "10px";
    pdfContainer.appendChild(title);

    // 3. Crear tabla de números vendidos/apartados
    const table = document.createElement("table");
    table.style.width = "100%";
    table.style.borderCollapse = "collapse";
    table.style.marginTop = "10px";

    // Crear fila de encabezado
    const headerRow = document.createElement("tr");
    ["Número", "Estado", "Comprador", "Teléfono"].forEach((text) => {
      const th = document.createElement("th");
      th.textContent = text;
      th.style.border = "1px solid #ddd";
      th.style.padding = "8px";
      th.style.textAlign = "left";
      headerRow.appendChild(th);
    });
    table.appendChild(headerRow);

    // Filtrar y agregar boletas vendidas/apartadas
    const boletasFiltradas = boletas.value.filter(
      (b) => b.estado === "vendida" || b.estado === "apartada"
    );

    boletasFiltradas.forEach((boleta) => {
      const row = document.createElement("tr");

      // Celda de número
      const numCell = document.createElement("td");
      numCell.textContent = boleta.numero;
      numCell.style.border = "1px solid #ddd";
      numCell.style.padding = "8px";
      row.appendChild(numCell);

      // Celda de estado
      const estadoCell = document.createElement("td");
      estadoCell.textContent =
        boleta.estado === "vendida" ? "Vendida" : "Apartada";
      estadoCell.style.border = "1px solid #ddd";
      estadoCell.style.padding = "8px";
      row.appendChild(estadoCell);

      // Celda de comprador
      const compradorCell = document.createElement("td");
      compradorCell.textContent = boleta.comprador || "N/A";
      compradorCell.style.border = "1px solid #ddd";
      compradorCell.style.padding = "8px";
      row.appendChild(compradorCell);

      // Celda de teléfono
      const telefonoCell = document.createElement("td");
      telefonoCell.textContent = boleta.telefono || "N/A";
      telefonoCell.style.border = "1px solid #ddd";
      telefonoCell.style.padding = "8px";
      row.appendChild(telefonoCell);

      table.appendChild(row);
    });

    pdfContainer.appendChild(table);

    // 4. Configuración del PDF
    const opt = {
      margin: [10, 10, 10, 10],
      filename: `Talonario_${rifaSeleccionada.value.nombre.replace(
        /[^a-z0-9]/gi,
        "_"
      )}_Reporte.pdf`,
      image: {
        type: "jpeg",
        quality: 1,
      },
      html2canvas: {
        scale: 2,
        logging: true,
        useCORS: true,
        allowTaint: true,
        letterRendering: true,
        backgroundColor: "#FFFFFF",
      },
      jsPDF: {
        unit: "mm",
        format: "a4",
        orientation: "portrait",
      },
    };

    // Generar y descargar el PDF
    await html2pdf().set(opt).from(pdfContainer).save();

    Swal.fire({
      title: "¡PDF Generado!",
      text: "El reporte se ha exportado correctamente",
      icon: "success",
      timer: 2000,
      showConfirmButton: false,
    });
  } catch (error) {
    console.error("Error al generar PDF:", error);
    Swal.fire({
      title: "Error",
      text: "No se pudo generar el PDF. Por favor intente nuevamente.",
      icon: "error",
    });
  }
};

// Watchers
watch(filtroEstado, () => {
  paginaActual.value = 1;
});

watch(busqueda, () => {
  paginaActual.value = 1;
});
</script>

<style scoped>
.container-fluid {
  width: 120vh !important;
  margin: 5vh 0;
}

.cursor-pointer {
  cursor: pointer;
}

.numero-boleta {
  min-height: 60px;
  display: flex;
  flex-direction: column;
  justify-content: center;
  border: 2px solid;
  transition: all 0.3s ease;
  font-size: 0.85rem;
}

.numero-boleta:hover {
  transform: scale(1.05);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.2);
}

.estado-text {
  font-size: 0.7rem;
  font-weight: bold;
}

.modal {
  background: rgba(0, 0, 0, 0.5);
}

.table td {
  vertical-align: middle;
}
.card {
  padding: 0 !important;
}

@media print {
  .numero-boleta {
    page-break-inside: avoid;
    min-height: 40px !important;
    font-size: 0.7rem !important;
  }

  .table {
    width: 100% !important;
  }

  body {
    padding: 10px !important;
    background: white !important;
  }

  /* Ocultar elementos no necesarios en PDF */
  .modal,
  .modal-backdrop,
  .btn,
  .breadcrumb,
  .input-group {
    display: none !important;
  }
}

/* Responsivo */
@media (max-width: 768px) {
  .numero-boleta {
    min-height: 50px;
    font-size: 0.75rem;
  }

  .estado-text {
    font-size: 0.6rem;
  }
}

/* Transiciones suaves */
* {
  transition: all 0.3s ease;
}
</style>
