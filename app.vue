<template>
  <div class="calendar" @click="flipDate">
    <div class="date-display" :class="{ flipped: isFlipped }">
      {{ displayDate }}
    </div>
    <button class="holiday-button" @click="goToNextHoliday">次の祝日へ</button>
  </div>
</template>

<script lang="ts">
import { ref, computed, onMounted } from "vue";

export default {
  setup() {
    // 現在の日付を保持するリアクティブな変数
    const currentDate = ref<Date>(new Date());
    // アニメーションの状態を保持するリアクティブな変数
    const isFlipped = ref<boolean>(false);
    // 祝日のリスト
    const holidays = ref<Date[]>([]);

    // 現在の日付をローカル形式で表示する計算プロパティ
    const displayDate = computed<string>(() =>
      currentDate.value.toLocaleDateString()
    );

    // APIから祝日を取得する関数
    const fetchHolidays = async (): Promise<void> => {
      try {
        const response = await fetch("http://api.national-holidays.jp/2025"); // 実際のAPIエンドポイントに置き換えてください
        const data = await response.json();
        console.log("祝日データ:", data);
        holidays.value = data.map((holiday: { date: string | number | Date; }) => new Date(holiday.date)); // APIから取得した日付をDateオブジェクトに変換
      } catch (error) {
        console.error("祝日の取得に失敗しました:", error);
      }
    };

    // 日付を進める関数
    const flipDate = (): void => {
      isFlipped.value = true; // アニメーション開始
      setTimeout(() => {
        const today = currentDate.value.getDate();
        const lastDayOfMonth = new Date(
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
      }, 500); // アニメーションの長さに合わせて調整
    };

    // 次の祝日に移動する関数
    const goToNextHoliday = (): void => {
      const today = currentDate.value;
      const nextHoliday = holidays.value.find((holiday) => holiday > today);
      if (nextHoliday) {
        currentDate.value = nextHoliday; // 次の祝日に変更
      } else {
        alert("今年の祝日はもうありません！");
      }
    };

    // コンポーネントがマウントされたときに祝日を取得
    onMounted(fetchHolidays);

    return {
      currentDate,
      isFlipped,
      displayDate,
      flipDate,
      goToNextHoliday,
    };
  },
};
</script>

<style scoped>
.calendar {
  cursor: pointer;
  text-align: center;
  margin: 20px;
}

.date-display {
  font-size: 2rem;
  padding: 20px;
  background-color: #f0f0f0;
  border: 1px solid #ccc;
  border-radius: 10px;
  transition: transform 0.5s ease-in-out;
}

.date-display.flipped {
  transform: rotateX(180deg); /* カレンダーがめくれるような回転アニメーション */
}

.holiday-button {
  margin-top: 10px;
  padding: 10px 20px;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.holiday-button:hover {
  background-color: #45a049;
}
</style>
