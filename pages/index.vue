<template>
  <div class="container text-center py-4">
    <div class="row g-4 justify-content-center">
      <div class="col-12 col-md-6">
        <GameCard title="Vòng quay nhậu" icon="🎡" description="Rượu vào lời ra, quay là phải uống!"
          @click="navigateTo('/spin-drink')" />
      </div>
      <div class="col-12 col-md-6">
        <GameCard title="Vòng quay lì xì" icon="🧧" description="May mắn đầu năm, nhận ngay lộc lớn!"
          @click="navigateTo('/spin-lixi')" />
      </div>
    </div>

    <div class="row g-4 mt-4">
      <div class="col-12 col-md-6">
        <div class="p-4 bg-white rounded-4 shadow-sm h-100 text-start">
          <h3 class="h6 fw-bold mb-3">Lịch sử Vòng quay nhậu</h3>
          <div v-if="drinkHistory.length === 0" class="text-muted small">
            Chưa có lượt quay nào.
          </div>
          <ul v-else class="list-unstyled mb-0 small">
            <li v-for="(item, index) in drinkHistory" :key="index" class="mb-2 pb-2 border-bottom last-child-no-border">
              <span class="badge bg-light text-dark me-2 font-monospace">{{ item.time }}</span>
              {{ item.result }}
            </li>
          </ul>
        </div>
      </div>
      <div class="col-12 col-md-6">
        <div class="p-4 bg-white rounded-4 shadow-sm h-100 text-start">
          <h3 class="h6 fw-bold mb-3">Lịch sử Vòng quay lì xì</h3>
          <div v-if="lixiHistory.length === 0" class="text-muted small">
            Chưa có lượt quay nào.
          </div>
          <ul v-else class="list-unstyled mb-0 small">
            <li v-for="(item, index) in lixiHistory" :key="index" class="mb-2 pb-2 border-bottom last-child-no-border">
              <span class="badge bg-light text-dark me-2 font-monospace">{{ item.time }}</span>
              {{ item.result }}
            </li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
const drinkHistory = ref([]);
const lixiHistory = ref([]);

onMounted(() => {
  const drinkSaved = localStorage.getItem('spin_history_drink');
  if (drinkSaved) {
    drinkHistory.value = JSON.parse(drinkSaved);
  }

  const lixiSaved = localStorage.getItem('spin_history_lixi');
  if (lixiSaved) {
    lixiHistory.value = JSON.parse(lixiSaved);
  }
});
</script>

<style scoped>
.last-child-no-border:last-child {
  border-bottom: none !important;
}
</style>
