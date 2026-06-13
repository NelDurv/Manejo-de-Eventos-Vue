<template>
  <div class="tarjeta-item" :data-id="id" @click="$emit('select', id)">
    <h3>{{ titulo }}</h3>
    <p>{{ descripcion }}</p>
    <button class="favorito-btn" @click.stop="$emit('favorite', id)">❤️ Favorito</button>
  </div>
</template>

<script setup>
defineProps({
  id: Number,
  titulo: String,
  descripcion: String,
})

defineEmits(['select', 'favorite'])
</script>

<style scoped>
.tarjeta-item {
  background: #ffffffdd;
  backdrop-filter: blur(2px);
  border: 1px solid #c8e0b5;
  border-radius: 1.2rem;
  padding: 1.2rem;
  cursor: pointer;
  transition: all 0.2s ease;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.03);
}

.tarjeta-item:hover {
  transform: translateY(-3px);
  border-color: #8fc96e;
  background: #fdfffa;
  box-shadow: 0 8px 18px rgba(0, 0, 0, 0.05);
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
  padding: 0.3rem 1rem;
  border-radius: 2rem;
}

.favorito-btn:hover {
  background: #e09d1f;
  color: white;
}
</style>
