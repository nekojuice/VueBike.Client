<script setup>
//import { ref } from 'vue'
const keywordsTyped = defineModel()
</script>
<template>
  <div class="">
    <input
      type="text"
      class="bg-light border border-secondary rounded-3 h-100 p-2"
      v-model="keywordsTyped"
      placeholder="搜尋站點地址"
    />
    <button class="btn btn-primary ms-2 h-100">查詢</button>
  </div>
</template>

<style scoped></style>
