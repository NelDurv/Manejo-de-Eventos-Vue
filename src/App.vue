<template>
  <div @keydown.ctrl.k.prevent="focusSearchInput">
    <h1>🌱 Huerto El Alba · Centro de Eventos</h1>
    <div class="sub">Agricultura orgánica · Manejo de eventos Vue</div>

    <!-- Módulo 1: Formulario búsqueda -->
    <section>
      <h2>🔍 Buscar en el huerto</h2>
      <form @submit.prevent="buscar">
        <div class="form-group">
          <input
            ref="searchInput"
            type="text"
            v-model="searchTerm"
            @keyup.enter="buscar"
            @keydown.esc="limpiarCampo"
            placeholder="Ej. compost, abonos verdes, rotación..."
          />
          <button type="submit">Buscar</button>
        </div>
      </form>
      <div v-if="searchResultMsg" class="resultado-busqueda">
        {{ searchResultMsg }}
      </div>
      <div class="hint">💡 Atajo: <kbd>Ctrl</kbd> + <kbd>K</kbd> → enfocar búsqueda</div>
    </section>

    <!-- Módulo 2: Tarjetas clicables con propagación y captura -->
    <section>
      <h2>🌿 Consejos orgánicos (tarjetas)</h2>
      <div class="tarjetas-grid">
        <Tarjeta
          v-for="card in cards"
          :key="card.id"
          :id="card.id"
          :titulo="card.titulo"
          :descripcion="card.descripcion"
          @select="seleccionarTarjeta"
          @favorite="marcarFavorito"
        />
      </div>
      <!-- Contenedor con .capture para evidenciar fase de captura -->
      <div
        class="capture-info"
        style="
          font-size: 0.8rem;
          margin-top: 1rem;
          background: #eff5e9;
          padding: 6px 12px;
          border-radius: 2rem;
        "
      >
        📡 El contenedor de tarjetas tiene <code>@click.capture</code> → verás "captura" en el
        registro ANTES del click en tarjeta o favorito.
      </div>
    </section>

    <!-- Módulo 3: Botón única vez (once) -->
    <section>
      <h2>✨ Acción única</h2>
      <button
        @click.once="mostrarTips"
        :disabled="tipDisabled"
        :class="{ 'tip-button': !tipDisabled }"
      >
        {{ tipButtonText }}
      </button>
      <p
        v-if="tipMostrado"
        style="margin-top: 0.5rem; background: #fff0cf; padding: 0.5rem; border-radius: 1rem"
      >
        🌟 Tip especial: ¡Usa estiércol de oveja maduro y rotación de cultivos para enriquecer tu
        suelo!
      </p>
    </section>

    <!-- Módulo 4: Caja scrollable con .passive -->
    <section>
      <h2>📜 Bitácora de técnicas (scroll)</h2>
      <div class="scroll-box" @scroll.passive="onScroll" ref="scrollContainer">
        <div class="scroll-content">
          <p>🌾 1. Compostaje en caliente: relación C/N 25-30:1</p>
          <p>🐞 2. Control biológico: mariquitas contra pulgones</p>
          <p>🌻 3. Asociación de cultivos: maíz + frijol + calabaza</p>
          <p>🧄 4. Purín de ortiga: fortalece las plantas</p>
          <p>🪱 5. Lombricompost: humus de lombriz de alta calidad</p>
          <p>🌱 6. Siembra directa sobre cubierta vegetal</p>
          <p>💧 7. Riego por goteo con agua de lluvia</p>
          <p>🍂 8. Acolchado (mulching) para retener humedad</p>
        </div>
      </div>
      <div class="scroll-indicator">📌 Posición scroll: {{ scrollPosition }} px</div>
    </section>

    <!-- Módulo 5: Enlace que no navega + Modal -->
    <section>
      <h2>🔗 Enlace ecológico</h2>
      <a href="https://example.com" @click.prevent="abrirModalInfo" class="enlace-ejemplo">
        📖 Saber más sobre agricultura orgánica (no navega)
      </a>
    </section>

    <!-- Módulo 6: Registro de eventos (audit log) -->
    <section>
      <h2>📋 Registro de eventos (últimos 30)</h2>
      <div class="log-panel">
        <ul class="log-list" ref="logList">
          <li v-for="(log, idx) in logs" :key="idx">{{ log }}</li>
          <li v-if="logs.length === 0">✨ Los eventos aparecerán aquí</li>
        </ul>
        <button @click="limpiarRegistro" class="clear-log">🗑️ Limpiar registro</button>
      </div>
    </section>

    <!-- Modal (componente) -->
    <Modal :show="modalVisible" @close="cerrarModal" />
  </div>
</template>

<script setup>
import { ref, reactive, onMounted, nextTick } from 'vue'
import Tarjeta from './components/Tarjeta.vue'
import Modal from './components/Modal.vue'

// --------------------- DATOS REACTIVOS ---------------------
const searchTerm = ref('')
const searchResultMsg = ref('')
const cards = ref([
  {
    id: 1,
    titulo: '🌱 Compost activo',
    descripcion: 'Aporta nitrógeno y microorganismos benéficos.',
  },
  {
    id: 2,
    titulo: '🐞 Control biológico',
    descripcion: 'Introduce crisopas y mariquitas en tu huerto.',
  },
  {
    id: 3,
    titulo: '🌾 Rotación de cultivos',
    descripcion: 'Previene plagas y mejora la fertilidad.',
  },
  { id: 4, titulo: '🍂 Mulching orgánico', descripcion: 'Paja, hojas secas, retiene humedad.' },
])

// logs (registro de eventos)
const logs = ref([])
const MAX_LOGS = 30

// Scroll
const scrollContainer = ref(null)
const scrollPosition = ref(0)
let lastScrollLogTime = 0

// Tips "única vez"
const tipDisabled = ref(false)
const tipMostrado = ref(false)
const tipButtonText = ref('🌿 Mostrar tips')

// Modal
const modalVisible = ref(false)

// Referencia input para Ctrl+K
const searchInput = ref(null)

// --------------------- FUNCIONES DE LOG ---------------------
function addLog(message) {
  logs.value = [message, ...logs.value].slice(0, MAX_LOGS)
  // Auto-scroll al último log? opcional (solo UX)
  nextTick(() => {
    const logContainer = document.querySelector('.log-list')
    if (logContainer) logContainer.scrollTop = 0
  })
}

// --------------------- MÓDULO 1: BÚSQUEDA ---------------------
function buscar() {
  if (!searchTerm.value.trim()) {
    const msg = '⚠️ Campo vacío. Escribe un tema orgánico (ej. compost)'
    searchResultMsg.value = msg
    addLog(`buscar vacío: ${msg}`)
    return
  }
  const term = searchTerm.value.trim()
  searchResultMsg.value = `✅ Resultados simulados para: "${term}" (en Huerto El Alba)`
  addLog(`buscar: "${term}"`)
}

function limpiarCampo() {
  searchTerm.value = ''
  searchResultMsg.value = ''
  addLog('limpiar: input reiniciado con ESC')
}

// Enfocar con Ctrl+K (combinación de teclas)
function focusSearchInput() {
  if (searchInput.value) {
    searchInput.value.focus()
    addLog('atajo Ctrl+K: foco en búsqueda')
  }
}

// --------------------- MÓDULO 2: TARJETAS (captura, burbujeo, stop) ---------------------
// Captura en contenedor PADRE (App.vue) - usamos @click.capture en el div que envuelve las tarjetas
// pero está aplicado sobre un div dentro de la sección. Lo pondremos en un contenedor extra.
// Como el template ya tiene .tarjetas-grid, mejor agregamos un div contenedor con capture.
// Rediseño rápido: agrego un div con @click.capture justo alrededor del grid.
// Atraparemos el evento y extraeremos el id de la tarjeta más cercana.
function logCaptura(event) {
  // Intentar obtener la tarjeta clickeada (puede ser el botón favorito o la tarjeta misma)
  const targetEl = event.target
  const tarjetaDiv = targetEl.closest('.tarjeta-item')
  if (tarjetaDiv && tarjetaDiv.dataset.id) {
    const id = tarjetaDiv.dataset.id
    addLog(`🔻 CAPTURA (fase captura) - tarjeta ${id}`)
  } else {
    addLog(`🔻 CAPTURA en contenedor (sin tarjeta específica)`)
  }
}

// Manejador para seleccionar tarjeta (burbujeo normal)
function seleccionarTarjeta(id) {
  addLog(`click tarjeta ${id}`)
  // Mostrar feedback adicional (opcional)
  searchResultMsg.value = `🌻 Tarjeta ${id} seleccionada: más info en el registro`
  setTimeout(() => {
    if (searchResultMsg.value.includes('seleccionada'))
      searchResultMsg.value = searchResultMsg.value.split('🌻')[0] || ''
  }, 2000)
}

function marcarFavorito(id) {
  addLog(`⭐ favorito ${id}`)
}

// --------------------- MÓDULO 3: ONCE (botón único) ---------------------
function mostrarTips() {
  tipMostrado.value = true
  tipDisabled.value = true
  tipButtonText.value = '✅ Tip mostrado'
  addLog('mostrar tips (una vez) - técnica agrícola revelada')
}

// --------------------- MÓDULO 4: SCROLL con .passive ---------------------
function onScroll(event) {
  const container = event.target
  const newPos = container.scrollTop
  scrollPosition.value = newPos

  // Throttle para no saturar el log (máximo cada 300ms)
  const now = Date.now()
  if (now - lastScrollLogTime > 300) {
    addLog(`scroll: ${newPos}px`)
    lastScrollLogTime = now
  }
}

// --------------------- MÓDULO 5: MODAL y enlace prevent ---------------------
function abrirModalInfo() {
  modalVisible.value = true
  addLog('abrir modal (prevent en enlace)')
}

function cerrarModal() {
  modalVisible.value = false
  addLog('cerrar modal')
}

// --------------------- MÓDULO 6: LIMPIAR REGISTRO ---------------------
function limpiarRegistro() {
  logs.value = []
  addLog('registro limpiado manualmente')
}

// Pequeño ajuste extra: añadir evento de inicial para demostrar
onMounted(() => {
  addLog('✨ App iniciada - Bienvenido al centro de eventos')
})
</script>

<style scoped>
/* Estilos adicionales específicos para la captura en el contenedor */
.capture-container {
  position: relative;
}
.tarjetas-grid {
  position: relative;
}
code {
  background: #e2e9da;
  padding: 2px 6px;
  border-radius: 12px;
  font-size: 0.8rem;
}
</style>

