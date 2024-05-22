<!-- eslint-disable vue/no-side-effects-in-computed-properties -->
<script setup>
import { computed, onMounted, ref } from 'vue'

// 資料  ----------
onMounted(async () => {
  console.log(`the component is now mounted.`)
  const response = await fetch(
    'https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json'
  )
  const data = await response.json()
  dataArray.value = data
})
const dataArray = ref([])

//--------------------
// Datatble ----------
// 站點搜尋關鍵字
const keywordsSearch = ref('')
// 資料搜尋-站點關鍵字
const dataSearched = computed(() =>
  dataSorted.value.filter((data) => data.ar.includes(keywordsSearch.value))
)
// 資料搜尋-關鍵字紅色醒目
function RedKeyword(origin) {
  if (!keywordsSearch.value) return origin
  const keyword = new RegExp(`(${keywordsSearch.value})`, 'gi') //g=全域, i=不分大小寫
  return origin.replace(keyword, '<span style="color: red">$1</span>')
}

// 資料切頁-一次10筆
const data10x = computed(() => {
  return dataSearched.value.slice(pageCurrent.value * 10, pageCurrent.value * 10 + 10)
})
// 資料排序-響應物件
const ref_sortToggle = ref({
  sortField: '', // 排序的欄位
  order: 0 // -1: desc，1: asc
})
// 資料排序-排序資料
function sortToggle(fieldName) {
  if (ref_sortToggle.value.sortField !== fieldName) {
    ref_sortToggle.value.sortField = fieldName
    ref_sortToggle.value.order = -1 // 初始為desc
  } else {
    ref_sortToggle.value.order *= -1 // 倒轉
  }
}
// 資料排序-按鈕圖示變換
function changeToggleIcon(fieldName) {
  if (ref_sortToggle.value.sortField == fieldName) {
    if (ref_sortToggle.value.order == -1)
      return '<i class="bi bi-caret-up"></i><br /><i class="bi bi-caret-down-fill"></i>'
    if (ref_sortToggle.value.order == 1)
      return '<i class="bi bi-caret-up-fill"></i><br /><i class="bi bi-caret-down"></i>'
  }
  return '<i class="bi bi-caret-up"></i><br /><i class="bi bi-caret-down"></i>'
}

// 資料排序-computed()
const dataSorted = computed(() => {
  let { sortField, order } = ref_sortToggle.value // 解構後失去ref，純計算應不影響
  return dataArray.value.sort((a, b) => (a[sortField] - b[sortField]) * order)
})

//--------------------
// Pagination --------
// 當前頁面
const pageCurrent = ref(0)
// 最大頁面
const pageMax = computed(() => Math.ceil(dataSearched.value.length / 10))

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
  pageCurrent.value = page - 1 // page值用於顯示，會多1
}
// 搜尋時回第一頁
function firstPageOnSearch() {
  btnPageJump(1)
}

// 頁面切換按鈕
const visiblePages = computed(() => {
  const pages = []
  const maxVisible = 10
  const half = Math.floor(maxVisible / 2) - 1

  let start = Math.max(pageCurrent.value - half, 1)
  let end = Math.min(start + maxVisible - 1, pageMax.value)

  if (end - start < maxVisible - 1) {
    start = Math.max(end - maxVisible + 1, 1)
  }

  for (let i = start; i <= end; i++) {
    pages.push(i)
  }
  return pages
})
// 當前頁面按鈕class顏色標記
function getButtonClass(page) {
  return page === pageCurrent.value + 1
    ? 'btn btn-primary rounded-0'
    : 'btn btn-outline-primary rounded-0'
}
</script>

<template>
  <main>
    <div class="container-fluid">
      <nav class="d-flex m-3">
        <span class="me-auto d-flex align-items-center fs-3 fw-bold">YouBike 站點查詢</span>
        <div class="">
          <input
            type="text"
            class="bg-light border border-secondary rounded-3 h-100 p-2"
            v-model="keywordsSearch"
            @input="firstPageOnSearch"
            placeholder="搜尋站點地址"
          />
          <button class="btn btn-primary ms-2 h-100">查詢</button>
        </div>
      </nav>
      <hr />
      <table class="table table-striped table-hover table-fixed align-middle text-center mb-5">
        <thead class="table-primary align-middle">
          <tr>
            <th class="p-2">站點編號</th>
            <th class="p-2">站點名稱</th>
            <th class="p-2">站點所在區域</th>
            <th class="p-2">站點地址</th>
            <th class="p-2">
              <div class="d-flex align-items-center justify-center">
                <span class="me-1">總車位數量</span>
                <span
                  class="hasCursor"
                  @click="sortToggle('total')"
                  v-html="changeToggleIcon('total')"
                >
                </span>
              </div>
            </th>
            <th class="p-2">
              <div class="d-flex align-items-center justify-center">
                <span class="me-1">可租借的腳踏車數量</span>
                <span
                  class="hasCursor"
                  @click="sortToggle('available_return_bikes')"
                  v-html="changeToggleIcon('available_return_bikes')"
                >
                </span>
              </div>
            </th>
            <th class="p-2">站點緯度</th>
            <th class="p-2">站點經度</th>
            <th class="p-2">可歸還的腳踏車數量</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="data in data10x" :key="data.sno">
            <td>{{ data.sno }}</td>
            <td>{{ data.sna }}</td>
            <td>{{ data.sarea }}</td>
            <td v-html="RedKeyword(data.ar.toString())"></td>
            <td>{{ data.total }}</td>
            <td>{{ data.available_return_bikes }}</td>
            <td>{{ data.latitude }}</td>
            <td>{{ data.longitude }}</td>
            <td>{{ data.available_rent_bikes }}</td>
          </tr>
        </tbody>
      </table>
      <div
        class="bg-light d-flex justify-content-center position-fixed bottom-0 start-50 translate-middle-x"
      >
        <button class="btn btn-outline-primary rounded-end-0" @click="btnPagePrev">上一頁</button
        ><button
          :class="getButtonClass(n)"
          @click="btnPageJump(n)"
          v-for="n in visiblePages"
          :key="n"
        >
          {{ n }}</button
        ><button class="btn btn-outline-primary rounded-start-0" @click="btnPageNext">
          下一頁
        </button>
        <!-- <div class="d-flex justify-content-center">{{ pageCurrent + 1 }} / {{ pageMax }}</div> -->
      </div>
    </div>
  </main>
</template>

<style scoped>
.hasCursor {
  cursor: pointer;
}
</style>
