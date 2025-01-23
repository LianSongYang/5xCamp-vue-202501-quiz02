<script setup>
import { ref, watch } from 'vue';
import flipCard from './components/flipCard.vue';

// 試完成以下功能：
//  1. 點擊卡片，卡片會翻開 (已完成)
//  2. 點擊兩張不同的卡片，卡片會翻回去 
//  3. 點擊兩張相同的卡片，卡片會消失
//  4. 當所有卡片都消失時，顯示「恭喜破關，再來一局？」的對話框，按下確定後重置遊戲
//  5. 將卡片獨立抽出為 Card.vue 元件

// 儲存所有卡片的數值
const cards = ref([]);
  // 儲存當前翻開的卡片索引
  const openedCard = ref([]);
  
  // 遊戲初始化函數
  const gameInit = () => {
    // 創建一個包含 1 到 16 的數組
    const numArr = [...new Array(16).keys()].map(i => ++i);
    // 隨機打亂數組順序
    numArr.sort(() => Math.random() - 0.5);
    // 將數字映射到 1-8 範圍內（每個數字會出現兩次）
    cards.value = numArr.map(d => (d % 8) + 1);
    // 重置已開啟的卡片
    openedCard.value = [];
  }

  // 卡片點擊處理函數
  const clickHandler = (idx) => {    
    // 檢查點擊的卡片是否已經在 openedCard 中
    const i = openedCard.value.indexOf(idx);
    // 獲取點擊卡片的數值
    const n = cards.value[idx];

    // 如果當前翻開的卡片少於兩張
    if (openedCard.value.length < 2) {
      if(i >= 0) {
        // 如果卡片已經翻開，則移除它
        openedCard.value.splice(i, 1);
      }
      else {
        // 否則，將卡片添加到已翻開列表
        openedCard.value.push(idx);
      }
    }

    // 檢查卡片是否匹配
    if (openedCard.value.length === 2) {
      if(cards.value[openedCard.value[0]] === cards.value[openedCard.value[1]]) {
        // 如果兩張卡片匹配
        window.setTimeout(() => {
          // 將匹配的卡片標記為 -1（表示已配對）
          cards.value = cards.value.map(d => (d === n) ? -1 : d);
          // 清空已翻開的卡片列表
          openedCard.value = [];
        }, 1000);
      }
      else {
        // 如果兩張卡片不匹配
        window.setTimeout(() => {
          // 1 秒後將卡片翻回去
          openedCard.value = [];
        }, 1000);
      }
    }
  }

  // 監視 cards 數組的變化
  watch(cards, v => {
    // 如果所有卡片都已配對（值為 -1）
    if(v.filter(d => d > 0).length === 0) {
      // 遊戲結束，詢問是否要再玩一局
      confirm('恭喜破關，再來一局？') && gameInit();
    }
  });
</script>


<template>
  <div class="bg-emerald-900 h-[100vh] w-full top-0 left-0 z-10 absolute">

    <div class="my-10 text-white text-center ">
      <h1 class="mb-8 text-5xl">五倍對對碰</h1>
      <button 
        @click="gameInit"
        class="rounded font-bold bg-blue-500 mx-6 text-white py-2 px-4 hover:bg-blue-700">
        {{ cards.length > 0 ? '重置' : '開始' }}
      </button>
    </div>

    <div 
      v-if="cards.length > 0"
      class="rounded-xl mx-auto border-4 mt-12 grid grid-flow-col p-10 w-[900px] gap-2 grid-rows-4">

      <flipCard 
        v-for="(n, idx) in cards" 
        :isOpened="openedCard.includes(idx)"
        :isMatch="cards[idx] === -1"
        :cardIdx="n"
        @click="clickHandler(idx)" />
    </div>
  </div>
</template>