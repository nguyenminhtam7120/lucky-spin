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
            <h1 class="h4 fw-black mb-0">Vòng quay lì xì 🧧</h1>
          </div>
        </div>
      </div>

      <div
        class="lixi-display flex-grow-1 my-3 d-flex flex-column align-items-center justify-content-center bg-white rounded-5 shadow-lg border border-danger border-5 mx-1">
        <div class="display-container d-flex align-items-center justify-content-center overflow-hidden w-100">
          <div v-if="!isSpinning && result === ''" class="fs-1 fw-black text-danger animate-pulse letter-spacing-2">
            ? ? ? , 0 0 0
          </div>
          <div v-else class="animated-numbers display-3 fw-black text-danger">
            {{ displayAmount.toLocaleString() }}<br><small class="fs-4">VND</small>
          </div>
        </div>
      </div>

      <div class="action-section mb-3">
        <button class="btn btn-danger btn-large-fun w-100 shadow-lg py-3 rounded-pill fw-semibold"
          :disabled="isSpinning" @click="spin">
          <span v-if="!isSpinning" class="fs-5">NHẬN LỘC</span>
          <span v-else class="fs-5">ĐANG CHỜ LỘC ...</span>
        </button>
        <!-- <p class="text-muted mt-2 small mb-0">Cầu được ước thấy, nhận lộc đầu năm!</p> -->
      </div>
    </div>

    <ResultModal :show="showModal" title="CHÚC MỪNG" :result="result + ' VND'" icon="🧧" @close="showModal = false" />
  </div>
</template>

<script setup>
import resultSound from '~/assets/sounds/result.mp3';

definePageMeta({
  layout: false
});
const isSpinning = ref(false);
const displayAmount = ref(10000);
const result = ref('');
const showModal = ref(false);

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
  result.value = '';

  const min = 10000;
  const max = 500000;
  const step = 10000;

  const finalAmount = Math.floor(Math.random() * ((max - min) / step + 1)) * step + min;
  const duration = 6000;
  const startTime = performance.now();

  let lastClickTime = 0;
  // const clickInterval = 100 // initial click speed in ms

  const animate = (currentTime) => {
    const elapsed = currentTime - startTime;
    const progress = Math.min(elapsed / duration, 1);

    // Update display amount rapidly
    if (progress < 1) {
      displayAmount.value = Math.floor(Math.random() * ((max - min) / step + 1)) * step + min;

      // Click speed increases steadily (interval decreases)
      const currentInterval = 150 - (progress * 120); // slows down from 150ms to 30ms
      if (currentTime - lastClickTime > currentInterval) {
        playClickSound();
        lastClickTime = currentTime;
      }

      requestAnimationFrame(animate);
    } else {
      isSpinning.value = false;

      // Play win sound
      if (winAudio) winAudio.play().catch(e => console.log('Audio play failed:', e));

      displayAmount.value = finalAmount;
      result.value = finalAmount.toLocaleString();

      saveHistory('Vòng quay lì xì', result.value + ' VND');
      showModal.value = true;
    }
  };

  requestAnimationFrame(animate);
};

const saveHistory = (game, res) => {
  const history = JSON.parse(localStorage.getItem('spin_history_lixi') || '[]');
  const now = new Date();
  const timeStr = `${now.getHours()}:${now.getMinutes().toString().padStart(2, '0')}`;

  history.unshift({ game, result: res, time: timeStr });
  localStorage.setItem('spin_history_lixi', JSON.stringify(history.slice(0, 5)));
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

.lixi-display {
  background-image: radial-gradient(circle, #fff 0%, #fff5f5 100%);
  position: relative;
  width: 100%;
}

.fw-black {
  font-weight: 900;
}

.letter-spacing-2 {
  letter-spacing: 2px;
}

.line-height-1 {
  line-height: 1;
}

.animate-pulse {
  animation: pulse 1.5s infinite;
}

@keyframes pulse {
  0% {
    transform: scale(1);
    opacity: 1;
  }

  50% {
    transform: scale(1.05);
    opacity: 0.8;
  }

  100% {
    transform: scale(1);
    opacity: 1;
  }
}
</style>
