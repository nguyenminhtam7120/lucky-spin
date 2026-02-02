<template>
  <div class="wheel-container">
    <div class="wheel-wrapper" :style="wheelStyle">
      <svg viewBox="0 0 100 100" class="wheel-svg">
        <g v-for="(option, index) in options" :key="index">
          <path :d="getSlicePath(index)" :fill="colors[index % colors.length]" class="wheel-slice" />
          <text :x="getTextPosition(index).x" :y="getTextPosition(index).y" :transform="getTextRotation(index)"
            text-anchor="middle" class="wheel-text">
            <tspan v-for="(line, lineIndex) in getLines(option)" :key="lineIndex" :x="getTextPosition(index).x"
              :dy="lineIndex === 0 ? '-0.5em' : '1.1em'">
              {{ line }}
            </tspan>
          </text>
        </g>
        <circle cx="50" cy="50" r="5" fill="white" />
      </svg>
    </div>
    <div class="wheel-pointer"></div>
  </div>
</template>

<script setup>
const props = defineProps({
  options: {
    type: Array,
    required: true
  },
  colors: {
    type: Array,
    default: () => [
      '#FF6B6B', '#4ECDC4', '#FFE66D', '#1A535C',
      '#F7FFF7', '#FF9F1C', '#2EC4B6', '#E71D36'
    ]
  },
  rotation: {
    type: Number,
    default: 0
  }
});

// Helper to split text into lines
const getLines = (text) => {
  if (!text) return [];
  // Split by space if text is long, or just return as is if short
  const words = text.split(' ');
  if (words.length <= 2) return [text];

  // Try to balance lines
  const mid = Math.ceil(words.length / 2);
  return [
    words.slice(0, mid).join(' '),
    words.slice(mid).join(' ')
  ];
};

const wheelStyle = computed(() => ({
  transform: `rotate(${props.rotation}deg)`,
  transition: 'none'
}));

const getSlicePath = (index) => {
  const total = props.options.length;
  const angle = 360 / total;
  const startAngle = index * angle;
  const endAngle = (index + 1) * angle;

  const x1 = 50 + 50 * Math.cos((startAngle - 90) * Math.PI / 180);
  const y1 = 50 + 50 * Math.sin((startAngle - 90) * Math.PI / 180);
  const x2 = 50 + 50 * Math.cos((endAngle - 90) * Math.PI / 180);
  const y2 = 50 + 50 * Math.sin((endAngle - 90) * Math.PI / 180);

  return `M 50 50 L ${x1} ${y1} A 50 50 0 0 1 ${x2} ${y2} Z`;
};

const getTextPosition = (index) => {
  const total = props.options.length;
  const angle = 360 / total;
  const midAngle = (index * angle + (index + 1) * angle) / 2;
  const r = 35; // Distance from center

  return {
    x: 50 + r * Math.cos((midAngle - 90) * Math.PI / 180),
    y: 50 + r * Math.sin((midAngle - 90) * Math.PI / 180)
  };
};

const getTextRotation = (index) => {
  const total = props.options.length;
  const angle = 360 / total;
  const midAngle = (index * angle + (index + 1) * angle) / 2;
  const pos = getTextPosition(index);
  return `rotate(${midAngle}, ${pos.x}, ${pos.y})`;
};
</script>

<style scoped>
.wheel-container {
  position: relative;
  width: 100%;
  max-width: 400px;
  aspect-ratio: 1;
  margin: 0 auto;
}

.wheel-wrapper {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  overflow: hidden;
  box-shadow: 0 0 25px rgba(0, 0, 0, 0.15);
  border: 8px solid white;
  will-change: transform;
  backface-visibility: hidden;
  transform: translateZ(0);
}

.wheel-svg {
  width: 100%;
  height: 100%;
  display: block;
}

.wheel-slice {
  stroke: rgba(255, 255, 255, 0.3);
  stroke-width: 0.2;
}

.wheel-text {
  fill: #FFFFFF;
  font-size: 3.8px;
  font-weight: 900;
  pointer-events: none;
  text-shadow: 1px 1px 2px rgba(0, 0, 0, 0.8);
}

.wheel-pointer {
  position: absolute;
  top: -10px;
  left: 50%;
  transform: translateX(-50%);
  width: 0;
  height: 0;
  border-left: 15px solid transparent;
  border-right: 15px solid transparent;
  border-top: 30px solid #E71D36;
  z-index: 10;
  filter: drop-shadow(0 2px 4px rgba(0, 0, 0, 0.3));
}
</style>
