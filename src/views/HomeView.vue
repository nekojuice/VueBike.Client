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
// 資料排序-按鈕切換
function sortToggle(fieldName) {
  if (ref_sortToggle.value.sortField !== fieldName) {
    ref_sortToggle.value.sortField = fieldName
    ref_sortToggle.value.order = -1 // 初始為desc
  } else {
    ref_sortToggle.value.order *= -1 // 倒轉
  }
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
</script>

<template>
  <main>
    <div class="container">
      <div>
        <button class="btn btn-primary">查詢</button>
        &nbsp;
        <input type="text" class="" v-model="keywordsSearch" @input="firstPageOnSearch" />
      </div>
      <table class="table">
        <thead>
          <tr>
            <th>站點編號</th>
            <th>站點名稱</th>
            <th>站點所在區域</th>
            <th>站點地址</th>
            <th>總車位數量<button @click="sortToggle('total')">S</button></th>
            <th>
              可租借的腳踏車數量<button @click="sortToggle('available_return_bikes')">S</button>
            </th>
            <th>站點緯度</th>
            <th>站點經度</th>
            <th>可歸還的腳踏車數量</th>
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
    </div>
    <div>
      <button class="btn btn-outline-primary" @click="btnPagePrev">上一頁</button
      ><button
        class="btn btn-outline-primary"
        @click="btnPageJump(n)"
        v-for="n in visiblePages"
        :key="n"
      >
        {{ n }}</button
      ><button class="btn btn-outline-primary" @click="btnPageNext">下一頁</button>
    </div>
    <div>{{ pageCurrent + 1 }} / {{ pageMax }}</div>
  </main>
</template>
