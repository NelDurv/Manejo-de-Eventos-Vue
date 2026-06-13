<template>
  <div v-if="show" class="modal-overlay" @click.self="emitClose">
    <div class="modal-container">
      <h2>🌾 Huerto El Alba · Agricultura Orgánica</h2>
      <p>
        🌿 <strong>Nuestra filosofía:</strong> Respeto por los ciclos naturales, abonos verdes,
        policultivos y biodiversidad. ¡Únete a la revolución ecológica!
      </p>
      <p>
        💧 Usamos riego eficiente, compost casero, rotación de cultivos y control biológico de
        plagas.
      </p>
      <button @click="emitClose" class="modal-close">Cerrar</button>
    </div>
  </div>
</template>

<script setup>
defineProps({
  show: Boolean,
})

const emit = defineEmits(['close'])

function emitClose() {
  emit('close')
}
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.6);
  backdrop-filter: blur(3px);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal-container {
  background: #ebffea;
  max-width: 450px;
  width: 90%;
  padding: 1.5rem;
  border-radius: 2rem;
  border-left: 12px solid #86bf6c;
  box-shadow: 0 20px 30px rgba(0, 0, 0, 0.2);
  text-align: center;
  animation: fadeSlide 0.2s ease;
}

.modal-container h2 {
  margin-top: 0;
  color: #2d6b2d;
}

.modal-close {
  margin-top: 1.2rem;
  background: #6fbf4c;
  color: white;
  border: none;
  padding: 0.5rem 1.2rem;
  border-radius: 2rem;
  cursor: pointer;
}

@keyframes fadeSlide {
  from {
    opacity: 0;
    transform: translateY(-20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>
