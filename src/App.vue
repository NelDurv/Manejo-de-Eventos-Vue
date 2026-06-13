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

    <!-- Módulo 2: Tarjeta única (consejos orgánicos) con ancho 100% -->
    <section>
      <h2>🌿 Consejo orgánico (eventos: captura, burbujeo, stop)</h2>
      <!-- Contenedor con .capture para demostrar fase de captura -->
      <div @click.capture="logCaptura" class="capture-wrapper">
        <div class="tarjeta-unica">
          <div class="tarjeta-con-feedback">
            <Tarjeta
              id="1"
              titulo="🌱 Compost activo"
              descripcion="Aporta nitrógeno y microorganismos benéficos."
              gradient="linear-gradient(135deg, #e6f7e6, #b8e0b8)"
              @select="seleccionarTarjeta"
              @favorite="marcarFavorito"
            />
            <div class="event-feedback">
              <span v-if="tarjetaEventMsg[1]"> 📢 {{ tarjetaEventMsg[1] }} </span>
              <span v-else class="text-muted">
                💡 Haz clic en la tarjeta o en "Favorito" para ver el evento
              </span>
            </div>
          </div>
        </div>
      </div>
      <div class="capture-info">
        📡 El contenedor gris tiene <code>@click.capture</code> → verás "🔻 CAPTURA" en el registro
        ANTES del click en tarjeta o favorito.
      </div>
    </section>

    <!-- SECCIÓN: Demostración de etapas de eventos (7 modificadores) -->
    <section>
      <h2>🎭 Etapas de eventos – Demostración visual</h2>
      <div class="demo-grid">
        <!-- @click normal -->
        <div class="demo-card">
          <h3>🔘 @click normal</h3>
          <button @click="demoClickNormal">Haz clic aquí</button>
          <div class="demo-feedback">{{ demoFeedback.normal }}</div>
          <small>El evento burbujea hacia el contenedor padre.</small>
        </div>

        <!-- @click.stop -->
        <div class="demo-card">
          <h3>🛑 @click.stop</h3>
          <div @click="demoClickPadre">
            <button @click.stop="demoClickStop">Haz clic aquí</button>
          </div>
          <div class="demo-feedback">{{ demoFeedback.stop }}</div>
          <small>El evento NO burbujea (detenido con .stop).</small>
        </div>

        <!-- @click.capture -->
        <div class="demo-card" @click.capture="demoCapture">
          <h3>📡 @click.capture</h3>
          <button @click="demoClickNormal">Haz clic aquí</button>
          <div class="demo-feedback">{{ demoFeedback.capture }}</div>
          <small>El evento se captura ANTES de llegar al botón.</small>
        </div>

        <!-- @click.once -->
        <div class="demo-card">
          <h3>✨ @click.once</h3>
          <button @click.once="demoOnce">Solo funciona una vez</button>
          <div class="demo-feedback">{{ demoFeedback.once }}</div>
          <small>El manejador se ejecuta una sola vez.</small>
        </div>

        <!-- @click.prevent -->
        <div class="demo-card">
          <h3>🔗 @click.prevent</h3>
          <a href="https://example.com" @click.prevent="demoPrevent">Enlace que no navega</a>
          <div class="demo-feedback">{{ demoFeedback.prevent }}</div>
          <small>Previene la navegación por defecto.</small>
        </div>

        <!-- @keyup.enter / @keydown.esc -->
        <div class="demo-card">
          <h3>⌨️ @keyup.enter + @keydown.esc</h3>
          <input
            type="text"
            @keyup.enter="demoEnter"
            @keydown.esc="demoEsc"
            placeholder="Escribe y pulsa Enter o Esc"
          />
          <div class="demo-feedback">{{ demoFeedback.keyboard }}</div>
          <small>Enter ejecuta búsqueda simulada, Esc limpia.</small>
        </div>

        <!-- @scroll.passive -->
        <div class="demo-card">
          <h3>📜 @scroll.passive</h3>
          <div class="mini-scroll" @scroll.passive="demoScroll">
            <div class="mini-content">
              <p>Línea 1</p>
              <p>Línea 2</p>
              <p>Línea 3</p>
              <p>Línea 4</p>
            </div>
          </div>
          <div class="demo-feedback">{{ demoFeedback.scroll }}</div>
          <small>Scroll pasivo (mejora rendimiento).</small>
        </div>
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
      <p v-if="tipMostrado" class="tip-message">
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

    <!-- Modal -->
    <Modal :show="modalVisible" @close="cerrarModal" />
  </div>
</template>

<script setup>
import { ref, onMounted, nextTick } from 'vue'
import Tarjeta from './components/Tarjeta.vue'
import Modal from './components/Modal.vue'

// --------------------- DATOS REACTIVOS ---------------------
const searchTerm = ref('')
const searchResultMsg = ref('')

// Ya no necesitamos array de cards, solo una tarjeta fija.
const tarjetaEventMsg = ref({})
const logs = ref([])
const MAX_LOGS = 30
const scrollContainer = ref(null)
const scrollPosition = ref(0)
let lastScrollLogTime = 0
const tipDisabled = ref(false)
const tipMostrado = ref(false)
const tipButtonText = ref('🌿 Mostrar tips')
const modalVisible = ref(false)
const searchInput = ref(null)

// Demo feedback
const demoFeedback = ref({
  normal: '',
  stop: '',
  capture: '',
  once: '',
  prevent: '',
  keyboard: '',
  scroll: '',
})
let onceTriggered = false

// --------------------- FUNCIONES DE LOG ---------------------
function addLog(message) {
  logs.value = [message, ...logs.value].slice(0, MAX_LOGS)
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

function focusSearchInput() {
  if (searchInput.value) {
    searchInput.value.focus()
    addLog('atajo Ctrl+K: foco en búsqueda')
  }
}

// --------------------- MÓDULO 2: TARJETA ÚNICA (captura, burbujeo, stop) ---------------------
function logCaptura(event) {
  const targetEl = event.target
  const tarjetaDiv = targetEl.closest('.tarjeta-item')
  if (tarjetaDiv && tarjetaDiv.dataset.id) {
    const id = parseInt(tarjetaDiv.dataset.id)
    addLog(`🔻 CAPTURA (fase captura) - Tarjeta ${id} (antes del click en la tarjeta)`)
    tarjetaEventMsg.value[id] = `🔻 Captura detectada (el evento pasó primero por el contenedor)`
    setTimeout(() => {
      if (tarjetaEventMsg.value[id]?.includes('Captura detectada')) {
        tarjetaEventMsg.value[id] = ''
      }
    }, 2000)
  } else {
    addLog(`🔻 CAPTURA en contenedor (sin tarjeta específica)`)
  }
}

function seleccionarTarjeta(id) {
  addLog(`click tarjeta ${id} (burbujeo normal)`)
  tarjetaEventMsg.value[id] = `✅ Evento: click en tarjeta (burbujeo normal)`
  setTimeout(() => {
    if (tarjetaEventMsg.value[id] === `✅ Evento: click en tarjeta (burbujeo normal)`) {
      tarjetaEventMsg.value[id] = ''
    }
  }, 3000)
  searchResultMsg.value = `🌻 Tarjeta ${id} seleccionada`
  setTimeout(() => {
    if (searchResultMsg.value.includes('seleccionada')) searchResultMsg.value = ''
  }, 2000)
}

function marcarFavorito(id) {
  addLog(`⭐ favorito ${id} (stop propagation - no burbujea hacia la tarjeta)`)
  tarjetaEventMsg.value[id] =
    `⭐ Evento: favorito (stop propagation - el click no llegó a la tarjeta)`
  setTimeout(() => {
    if (tarjetaEventMsg.value[id]?.startsWith('⭐')) {
      tarjetaEventMsg.value[id] = ''
    }
  }, 3000)
}

// --------------------- MÓDULO 3: ONCE ---------------------
function mostrarTips() {
  tipMostrado.value = true
  tipDisabled.value = true
  tipButtonText.value = '✅ Tip mostrado'
  addLog('mostrar tips (una vez) - técnica agrícola revelada')
}

// --------------------- MÓDULO 4: SCROLL ---------------------
function onScroll(event) {
  const container = event.target
  const newPos = container.scrollTop
  scrollPosition.value = newPos
  const now = Date.now()
  if (now - lastScrollLogTime > 300) {
    addLog(`scroll: ${newPos}px`)
    lastScrollLogTime = now
  }
}

// --------------------- MÓDULO 5: MODAL ---------------------
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

// --------------------- DEMOSTRACIÓN DE EVENTOS ---------------------
function demoClickNormal() {
  const msg = 'Evento @click normal (burbujeo)'
  demoFeedback.value.normal = msg
  addLog(msg)
  setTimeout(() => {
    if (demoFeedback.value.normal === msg) demoFeedback.value.normal = ''
  }, 2000)
}

function demoClickStop() {
  const msg = 'Evento @click.stop (propagación detenida)'
  demoFeedback.value.stop = msg
  addLog(msg)
  setTimeout(() => {
    if (demoFeedback.value.stop === msg) demoFeedback.value.stop = ''
  }, 2000)
}

function demoClickPadre() {
  addLog('Contenedor padre recibió el click (esto NO debería ocurrir con .stop)')
}

function demoCapture() {
  const msg = 'Fase de CAPTURA: evento capturado antes de llegar al botón'
  demoFeedback.value.capture = msg
  addLog(msg)
  setTimeout(() => {
    if (demoFeedback.value.capture === msg) demoFeedback.value.capture = ''
  }, 2000)
}

function demoOnce() {
  if (!onceTriggered) {
    const msg = 'Evento @click.once ejecutado (solo una vez)'
    demoFeedback.value.once = msg
    addLog(msg)
    onceTriggered = true
    setTimeout(() => {
      if (demoFeedback.value.once === msg) demoFeedback.value.once = ''
    }, 2000)
  }
}

function demoPrevent() {
  const msg = 'Evento @click.prevent - navegación cancelada'
  demoFeedback.value.prevent = msg
  addLog(msg)
  setTimeout(() => {
    if (demoFeedback.value.prevent === msg) demoFeedback.value.prevent = ''
  }, 2000)
}

function demoEnter(event) {
  const valor = event.target.value
  const msg = `@keyup.enter: buscando "${valor}"`
  demoFeedback.value.keyboard = msg
  addLog(msg)
  setTimeout(() => {
    if (demoFeedback.value.keyboard === msg) demoFeedback.value.keyboard = ''
  }, 2000)
}

function demoEsc(event) {
  event.target.value = ''
  const msg = '@keydown.esc: campo limpiado'
  demoFeedback.value.keyboard = msg
  addLog(msg)
  setTimeout(() => {
    if (demoFeedback.value.keyboard === msg) demoFeedback.value.keyboard = ''
  }, 2000)
}

function demoScroll(event) {
  const top = event.target.scrollTop
  const msg = `@scroll.passive: posición ${top}px`
  demoFeedback.value.scroll = msg
  const now = Date.now()
  if (now - lastScrollLogTime > 500) {
    addLog(msg)
    lastScrollLogTime = now
  }
  setTimeout(() => {
    if (demoFeedback.value.scroll === msg) demoFeedback.value.scroll = ''
  }, 1500)
}

onMounted(() => {
  addLog('✨ App iniciada - Bienvenido al centro de eventos')
})
</script>

<style scoped>
/* Estilos específicos para la sección de tarjeta única */
.capture-wrapper {
  background: #f9fbf4;
  padding: 0.5rem;
  border-radius: 1rem;
  border: 1px dashed #bdd4a3;
}

.tarjeta-unica {
  width: 100%;
  max-width: 100%;
}

.tarjeta-con-feedback {
  display: flex;
  flex-direction: column;
  width: 100%;
}

.event-feedback {
  background: #fef7e0;
  border-radius: 1rem;
  padding: 0.5rem 0.8rem;
  margin-top: 0.6rem;
  font-size: 0.8rem;
  text-align: center;
  border-left: 4px solid #86bf6c;
  min-height: 65px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.text-muted {
  color: #8a9a78;
  font-style: italic;
}

.capture-info {
  font-size: 0.8rem;
  margin-top: 1rem;
  background: #eaf3e2;
  padding: 6px 12px;
  border-radius: 2rem;
  text-align: center;
}

.demo-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(280px, 1fr));
  gap: 1.2rem;
  margin-top: 1rem;
}

.demo-card {
  background: #b5c9f3;
  border: 1px solid #dce5ce;
  border-radius: 1rem;
  padding: 1rem;
}

.demo-card h3 {
  margin: 0 0 0.5rem 0;
  font-size: 1.1rem;
  color: #4c764c;
}

.demo-card button,
.demo-card a,
.demo-card input {
  margin: 0.5rem 0;
  width: 100%;
}

.demo-feedback {
  background: #eef3e7;
  border-radius: 0.8rem;
  padding: 0.4rem;
  font-size: 0.8rem;
  text-align: center;
  color: #2a5c2a;
  min-height: 48px;
  margin-top: 0.5rem;
}

.mini-scroll {
  height: 80px;
  overflow-y: auto;
  background: #ffffff;
  border: 1px solid #ccc;
  border-radius: 0.5rem;
  padding: 0.3rem;
  font-size: 0.8rem;
}

.mini-content p {
  margin: 0.2rem 0;
}

small {
  font-size: 0.7rem;
  color: #6b7c5e;
  display: block;
  margin-top: 0.4rem;
}

.tip-message {
  margin-top: 0.5rem;
  background: #fff0cf;
  padding: 0.5rem;
  border-radius: 1rem;
}

code {
  background: #e2e9da;
  padding: 2px 6px;
  border-radius: 12px;
  font-size: 0.8rem;
}
</style>
