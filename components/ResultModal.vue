<template>
  <div v-if="show" class="custom-modal-overlay d-flex align-items-center justify-content-center" @click.self="close">
    <div class="modal-dialog modal-dialog-centered p-3 w-100" style="max-width: 350px;">
      <div class="modal-content text-center p-4 shadow-lg border-0 rounded-5 animate-pop-in">
        <div class="modal-header border-0 justify-content-center p-0 mb-2">
          <h5 class="modal-title fw-bolder fs-6 fw-normal text-uppercase letter-spacing-1">{{ title }}</h5>
        </div>
        <div class="modal-body py-3">
          <div class="display-2 mb-3 animate-bounce">{{ icon }}</div>
          <div class="fs-1 fw-black text-primary lh-sm">{{ result }}</div>
        </div>
        <div class="modal-footer border-0 justify-content-center p-0 mt-2">
          <button type="button" class="btn btn-primary btn-lg w-100 rounded-pill fw-bold shadow-sm py-3" @click="close">
            OK XONG!
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  show: Boolean,
  title: String,
  result: String,
  icon: {
    type: String,
    default: '🎉'
  }
});

const emit = defineEmits(['close']);

const close = () => {
  emit('close');
};
</script>

<style scoped>
.custom-modal-overlay {
  background-color: rgba(0, 0, 0, 0.8);
  backdrop-filter: blur(4px);
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  z-index: 1050;
  padding: 20px;
}

.modal-content {
  background-color: #ffffff !important;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3) !important;
  backface-visibility: hidden;
  -webkit-font-smoothing: antialiased;
  transform: translateZ(0);
}

.fw-black {
  font-weight: 900;
}

.letter-spacing-1 {
  letter-spacing: 1px;
}

.animate-pop-in {
  animation: pop-in 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275) forwards;
}

.animate-bounce {
  animation: bounce 2s infinite;
}

@keyframes pop-in {
  0% {
    transform: scale(0.8) translateZ(0);
    opacity: 0;
  }

  100% {
    transform: scale(1) translateZ(0);
    opacity: 1;
  }
}

@keyframes bounce {

  0%,
  20%,
  50%,
  80%,
  100% {
    transform: translateY(0) translateZ(0);
  }

  40% {
    transform: translateY(-20px) translateZ(0);
  }

  60% {
    transform: translateY(-10px) translateZ(0);
  }
}
</style>
