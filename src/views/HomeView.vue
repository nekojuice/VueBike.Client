<script setup>
import { computed, onMounted, ref } from 'vue'

// 資料
onMounted(async () => {
  console.log(`the component is now mounted.`)
  const response = await fetch(
    'https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json'
  )
  const data = await response.json()
  dataArray.value = data
})
const dataArray = ref([])

// Datatble
// 過濾顯示資料(換頁)
const dataFiltered = computed(() =>
  dataArray.value.slice(pageCurrent.value * 10, pageCurrent.value * 10 + 10)
)

// Pages
// 當前頁面
const pageCurrent = ref(0)
// 最大頁面
const pageMax = computed(() => Math.ceil(dataArray.value.length / 10))

// 頁面切換
function btnPagePrev() {
  if (pageCurrent.value <= 0) {
    return
  }
  pageCurrent.value--
}
function btnPageNext() {
  if (pageCurrent.value >= pageMax.value) {
    return
  }
  pageCurrent.value++
}
function btnPageJump(page) {
  pageCurrent.value = page - 1 // page用於顯示，多1
}
</script>

<template>
  <main>
    <div class="container">
      <div>
        <button></button>
        <input type="text" />
      </div>
      <table class="table">
        <thead>
          <tr>
            <th>站點編號</th>
            <th>站點名稱</th>
            <th>站點所在區域</th>
            <th>站點地址</th>
            <th>總車位數量</th>
            <th>可租借的腳踏車數量</th>
            <th>站點緯度</th>
            <th>站點經度</th>
            <th>可歸還的腳踏車數量</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="data in dataFiltered" v-bind:key="data.sno">
            <td>{{ data.sno }}</td>
            <td>{{ data.sna }}</td>
            <td>{{ data.sarea }}</td>
            <td>{{ data.ar }}</td>
            <td>{{ data.total }}</td>
            <td>{{ data.available_return_bikes }}</td>
            <td>{{ data.latitude }}</td>
            <td>{{ data.longitude }}</td>
            <td>{{ data.available_rent_bikes }}</td>
          </tr>
        </tbody>
      </table>
    </div>
    <div>
      <button class="btn btn-outline-primary" @click="btnPagePrev">上一頁</button
      ><button
        class="btn btn-outline-primary"
        @click="btnPageJump(n)"
        v-for="n in pageMax"
        v-bind:key="n"
      >
        {{ n }}</button
      ><button class="btn btn-outline-primary" @click="btnPageNext">下一頁</button>
    </div>
    <div>{{ pageCurrent + 1 }} / {{ pageMax }}</div>
  </main>
</template>
