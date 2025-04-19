<!-- <template>
  <div>
    <NuxtRouteAnnouncer />
    <NuxtWelcome />
  </div>
</template> -->
<template>
  <div class="calendar" @click="flipDate">
    <div class="date-display" :class="{ flipped: isFlipped.value }">{{ displayDate }}</div>
  </div>
</template>

<script>
import { ref, computed } from 'vue';

export default {
  setup() {
    const currentDate = ref(new Date());
    const isFlipped = ref(false);

    const displayDate = computed(() => currentDate.value.toLocaleDateString());

    const flipDate = () => {
      isFlipped.value = !isFlipped.value; // アニメーション開始
      setTimeout(() => {
        const today = currentDate.value.getDate();
        const lastDayOfMonth = new Date(currentDate.value.getFullYear(), currentDate.value.getMonth() + 1, 0).getDate();

        // 月末かどうかをチェック
        if (today === lastDayOfMonth) {
          // 翌月の1日にリセット
          currentDate.value = new Date(currentDate.value.getFullYear(), currentDate.value.getMonth() + 1, 1);
        } else {
          // 翌日に進める（新しい Date オブジェクトを作成）
          currentDate.value = new Date(currentDate.value.getFullYear(), currentDate.value.getMonth(), today + 1);
        }

        isFlipped.value = false; // アニメーション終了
      }, 500); // 500msはアニメーションの長さに合わせて調整
    };

    return {
      currentDate,
      isFlipped,
      displayDate,
      flipDate
    };
  }
};
</script>

<style scoped>
.calendar {
  cursor: pointer;
  /* カレンダーのスタイル */
}
.date-display {
  transition: transform 0.5s ease-in-out;
}
.date-display.flipped {
  transform: rotateX(180deg); /* カレンダーがめくれるような回転アニメーション */
}
</style>
