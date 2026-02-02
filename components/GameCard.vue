<template>
  <div class="card shadow-sm h-100 text-center p-4 cursor-pointer" @click="$emit('click')">
    <div class="card-body d-flex flex-column align-items-center justify-content-center">
      <div class="display-1 mb-3">{{ icon }}</div>
      <h2 class="card-title fw-bold h4">{{ title }}</h2>
      <p class="card-text text-muted">{{ description }}</p>
    </div>
  </div>
</template>

<script setup>
defineProps({
  title: String,
  icon: String,
  description: String
});

defineEmits(['click']);
</script>

<style scoped>
.cursor-pointer {
  cursor: pointer;
}

/* .card:hover {
  transform: translateY(-5px);
  background-color: #fff;
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
} */
</style>
