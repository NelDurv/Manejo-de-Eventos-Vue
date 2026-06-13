<template>
  <div
    class="tarjeta-item"
    :data-id="id"
    :style="{ background: gradient }"
    @click="$emit('select', id)"
  >
    <h3>{{ titulo }}</h3>
    <p>{{ descripcion }}</p>
    <button class="favorito-btn" @click.stop="$emit('favorite', id)">
      🚫 @click.stop (evita burbujeo)
    </button>
  </div>
</template>

<script setup>
defineProps({
  id: Number,
  titulo: String,
  descripcion: String,
  gradient: {
    type: String,
    default: 'linear-gradient(135deg, #ffffff, #f0f7ea)',
  },
})

defineEmits(['select', 'favorite'])
</script>

<style scoped>
.tarjeta-item {
  border-radius: 1.2rem;
  padding: 1.2rem;
  cursor: pointer;
  transition:
    transform 0.2s ease,
    box-shadow 0.2s ease;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.05);
  border: 1px solid rgba(200, 224, 181, 0.6);
  width: 100%;
}

.tarjeta-item:hover {
  transform: translateY(-4px);
  box-shadow: 0 12px 20px rgba(0, 0, 0, 0.1);
  border-color: #8fc96e;
}

.tarjeta-item h3 {
  margin: 0 0 0.5rem 0;
  color: #2d6a2d;
}

.tarjeta-item p {
  margin: 0 0 1rem 0;
  font-size: 0.9rem;
  color: #3b4e34;
}

.favorito-btn {
  background: #f3b33d;
  color: #2c2b26;
  font-size: 0.8rem;
  padding: 0.4rem 1rem;
  border-radius: 2rem;
  border: none;
  font-weight: bold;
  cursor: pointer;
  transition: 0.2s;
}

.favorito-btn:hover {
  background: #e09d1f;
  color: white;
  transform: scale(0.98);
}
</style>
