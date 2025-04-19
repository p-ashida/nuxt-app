<template>
  <div class="calendar" @click="flipDate">
    <div class="date-display" :class="{ flipped: isFlipped }">{{ displayDate }}</div>
  </div>
</template>

<script lang="ts">
import { ref, computed } from 'vue';

export default {
  setup() {
    // 現在の日付を保持するリアクティブな変数
    const currentDate = ref<Date>(new Date());
    // アニメーションの状態を保持するリアクティブな変数
    const isFlipped = ref<boolean>(false);

    // 現在の日付をローカル形式で表示する計算プロパティ
    const displayDate = computed<string>(() => currentDate.value.toLocaleDateString());

    // 日付を進める関数
    const flipDate = (): void => {
      isFlipped.value = !isFlipped.value; // アニメーション開始
      setTimeout(() => {
        const today: number = currentDate.value.getDate();
        const lastDayOfMonth: number = new Date(
          currentDate.value.getFullYear(),
          currentDate.value.getMonth() + 1,
          0
        ).getDate();

        // 月末かどうかをチェック
        if (today === lastDayOfMonth) {
          // 翌月の1日にリセット
          currentDate.value = new Date(
            currentDate.value.getFullYear(),
            currentDate.value.getMonth() + 1,
            1
          );
        } else {
          // 翌日に進める（新しい Date オブジェクトを作成）
          currentDate.value = new Date(
            currentDate.value.getFullYear(),
            currentDate.value.getMonth(),
            today + 1
          );
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