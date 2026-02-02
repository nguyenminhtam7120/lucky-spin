<template>
  <div class="game-container">
    <div class="container-fluid h-100 text-center d-flex flex-column justify-content-between p-3">
      <div class="header-section mt-2">
        <div class="d-flex align-items-center">
          <NuxtLink to="/" class="btn border-0 shadow-none p-2 line-height-1">
            <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 24 24" fill="none"
              stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
              <line x1="19" y1="12" x2="5" y2="12"></line>
              <polyline points="12 19 5 12 12 5"></polyline>
            </svg>
          </NuxtLink>
          <div class="flex-grow-1 me-4">
            <h1 class="h4 fw-black mb-0">Vòng quay nhậu 🎡</h1>
          </div>
        </div>
        <p class="text-danger small mb-0">Đã uống rượu bia thì không lái xe</p>
      </div>

      <div class="wheel-section flex-grow-1 d-flex align-items-center justify-content-center py-2">
        <SpinWheel :options="options" :colors="wheelColors" :spinning="isSpinning" :rotation="currentRotation" />
      </div>

      <div class="action-section mb-3">
        <button class="btn btn-primary btn-large-fun w-100 shadow-lg py-3 rounded-pill fw-semibold"
          :disabled="isSpinning" @click="spin">
          <span v-if="!isSpinning" class="fs-5">QUAY NGAY</span>
          <span v-else class="fs-5">ĐANG QUAY ...</span>
        </button>
        <!-- <p class="text-muted mt-2 small mb-0">Không say không về, vui là chính!</p> -->
      </div>
    </div>

    <ResultModal :show="showModal" title="KẾT QUẢ" :result="result" icon="🍺" @close="showModal = false" />
  </div>
</template>

<script setup>
import resultSound from '~/assets/sounds/result.mp3';

definePageMeta({
  layout: false
});
const options = [
  'Skip 1 vòng',
  'Uống 1/2 ly',
  'Uống hết ly',
  'Uống 2 ly',
  'Skip 2 người',
  'Chọn người uống thay',
  'Uống cùng bên trái',
  'Uống cùng bên phải',
];

const wheelColors = [
  '#0d6efd', // Primary (Blue)
  '#198754', // Success (Green)
  '#dc3545', // Danger (Red)
  '#ffc107', // Warning (Yellow)
  '#0dcaf0', // Info (Cyan)
  '#212529', // Dark
  '#6c757d', // Secondary (Gray)
  '#800080', // Purple
];

const isSpinning = ref(false);
const currentRotation = ref(0);
const showModal = ref(false);
const result = ref('');

// Audio references
let winAudio = null;
let audioCtx = null;

const playClickSound = () => {
  try {
    if (!audioCtx) {
      audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    }
    if (audioCtx.state === 'suspended') {
      audioCtx.resume();
    }
    const osc = audioCtx.createOscillator();
    const gain = audioCtx.createGain();
    osc.type = "square";
    osc.frequency.setValueAtTime(900, audioCtx.currentTime);
    gain.gain.setValueAtTime(0.2, audioCtx.currentTime);
    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.05);
    osc.connect(gain);
    gain.connect(audioCtx.destination);
    osc.start();
    osc.stop(audioCtx.currentTime + 0.05);
  } catch (e) {
    console.error('Web Audio API error:', e);
  }
};

onMounted(() => {
  // Using imported sound for result
  winAudio = new Audio(resultSound);
  winAudio.preload = 'auto';
  winAudio.volume = 0.4;
});

const spin = () => {
  if (isSpinning.value) return;

  isSpinning.value = true;

  const startRotation = currentRotation.value;
  const extraDegrees = Math.floor(Math.random() * 360);
  const totalDegrees = 360 * (5 + Math.floor(Math.random() * 5)) + extraDegrees;
  // const endRotation = startRotation + totalDegrees

  const duration = 6000;
  const startTime = performance.now();

  const sliceAngle = 360 / options.length;
  let lastClickIndex = Math.floor(startRotation / sliceAngle);

  const animate = (currentTime) => {
    const elapsed = currentTime - startTime;
    const progress = Math.min(elapsed / duration, 1);

    // Cubic-bezier easing (approximate ease-out)
    const easeOut = 1 - Math.pow(1 - progress, 3);
    const currentPos = startRotation + totalDegrees * easeOut;

    currentRotation.value = currentPos;

    // Calculate click sound
    const currentClickIndex = Math.floor(currentPos / sliceAngle);
    if (currentClickIndex !== lastClickIndex) {
      playClickSound();
      lastClickIndex = currentClickIndex;
    }

    if (progress < 1) {
      requestAnimationFrame(animate);
    } else {
      isSpinning.value = false;

      // Play win sound
      if (winAudio) winAudio.play().catch(e => console.log('Audio play failed:', e));

      const normalizedRotation = currentRotation.value % 360;
      const index = Math.floor((360 - (normalizedRotation % 360)) / sliceAngle) % options.length;
      result.value = options[index];

      saveHistory('Vòng quay nhậu', result.value);
      showModal.value = true;
    }
  };

  requestAnimationFrame(animate);
};

const saveHistory = (game, res) => {
  const history = JSON.parse(localStorage.getItem('spin_history_drink') || '[]');
  const now = new Date();
  const timeStr = `${now.getHours()}:${now.getMinutes().toString().padStart(2, '0')}`;

  history.unshift({ game, result: res, time: timeStr });
  localStorage.setItem('spin_history_drink', JSON.stringify(history.slice(0, 5)));
};
</script>

<style scoped>
.game-container {
  width: 100vw;
  height: 100dvh;
  overflow: hidden;
  background-color: #fcfcfc;
  position: fixed;
  top: 0;
  left: 0;
}

.wheel-section {
  width: 100%;
  max-width: 450px;
  margin: 0 auto;
}

.fw-black {
  font-weight: 900;
}

.line-height-1 {
  line-height: 1;
}
</style>
