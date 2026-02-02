<template>
  <div id="luckySpin">
    <NuxtLayout>
      <NuxtPage />
    </NuxtLayout>
  </div>
</template>

<style lang="scss">
#luckySpin {

  .page-enter-active,
  .page-leave-active {
    transition: all 0.3s ease-out;
  }

  .page-enter-from,
  .page-leave-to {
    opacity: 0;
    transform: translateY(20px);
  }

  background: #f2f2f2;
}
</style>
