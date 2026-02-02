<template>
  <div class="d-flex flex-column min-vh-100">
    <main class="flex-grow-1 py-4 container">
      <slot />
    </main>
    <footer class="py-3 text-center mt-auto">
      <p class="text-muted mb-0 small">© 2026 - Lucky Spin developed by Hades Jr</p>
    </footer>
  </div>
</template>

<style scoped>
.navbar-brand {
  letter-spacing: 1px;
}
</style>
