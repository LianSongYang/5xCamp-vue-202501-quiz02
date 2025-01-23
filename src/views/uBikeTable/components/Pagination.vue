<script setup>
import { computed } from "vue";

const props = defineProps({
  totalPageCount: {
    type: Number,
    default: 0,
  },
  currentPage: {
    type: Number,
    default: 1,
  },
});

const emit = defineEmits(["update:currentPage"]);

// 頁碼最多顯示幾格 (也可以寫成 props)
const PAGINATION_MAX = 10;

// 為了不用改寫原本的程式碼，所以用 computed 來包覆 props
const totalPageCount = computed(() => props.totalPageCount);
const currentPage = computed(() => props.currentPage);

// 事件
const setPage = (val) => emit("update:currentPage", val);

// 分頁的尾端
const pagerEnd = computed(() => {
  return totalPageCount.value <= PAGINATION_MAX
    ? totalPageCount.value
    : PAGINATION_MAX;
});

// 分頁的位移，用來確保目前的頁碼在中間
const pagerAddAmount = computed(() => {
  const tmp =
    totalPageCount.value <= PAGINATION_MAX
      ? 0
      : currentPage.value + 4 - pagerEnd.value;
  return tmp <= 0
    ? 0
    : totalPageCount.value - (PAGINATION_MAX + tmp) < 0
    ? totalPageCount.value - PAGINATION_MAX
    : tmp;
});
</script>

<template>
  <nav v-if="pagerEnd > 0">
    <ul class="pagination">
      <li @click.prevent="setPage(currentPage - 1)" class="page-item">
        <a class="page-link" href>Previous</a>
      </li>

      <li
        v-for="i in pagerEnd"
        :class="{ active: i + pagerAddAmount === currentPage }"
        :key="i"
        @click.prevent="setPage(i + pagerAddAmount)"
        class="page-item"
      >
        <a class="page-link" href>{{ i + pagerAddAmount }}</a>
      </li>

      <li @click.prevent="setPage(currentPage + 1)" class="page-item">
        <a class="page-link" href>Next</a>
      </li>
    </ul>
  </nav>
</template>

<style scoped>
.pagination {
  display: flex;
  justify-content: center;
}
</style>
