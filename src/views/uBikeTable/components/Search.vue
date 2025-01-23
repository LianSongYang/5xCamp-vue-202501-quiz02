<script setup>
import { computed } from 'vue';

const searchText = defineModel('searchText');
</script>

<template>
    <p>
        站點名稱搜尋: <input type="text" class="border" v-model="searchText">
    </p>
</template>