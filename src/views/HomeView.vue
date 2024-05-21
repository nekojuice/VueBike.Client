<script setup>
import { computed, onMounted } from 'vue'

// # 資料格式
// {
//   "sno": "500101001",
//   "sna": "YouBike2.0_捷運科技大樓站",
//   "sarea": "大安區",
//   "mday": "2024-05-21 10:24:15",
//   "ar": "復興南路二段235號前",
//   "sareaen": "Daan Dist.",
//   "snaen": "YouBike2.0_MRT Technology Bldg. Sta.",
//   "aren": "No.235， Sec. 2， Fuxing S. Rd.",
//   "act": "1",
//   "srcUpdateTime": "2024-05-21 10:32:24",
//   "updateTime": "2024-05-21 10:32:51",
//   "infoTime": "2024-05-21 10:24:15",
//   "infoDate": "2024-05-21",
//   "total": 28,
//   "available_rent_bikes": 0,
//   "latitude": 25.02605,
//   "longitude": 121.5436,
//   "available_return_bikes": 28
// }

import { ref } from 'vue'

// 假資料，以後替換
const dataArray = ref([
  { sno: 500101001, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  {
    sno: 500101002,
    sna: 'YouBike2.0_復興南路二段273號前',
    sarea: '大安區',
    ar: '復興南路二段273號西側'
  },
  { sno: 500101003, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101004, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101005, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101006, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101007, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101008, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101009, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101010, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101011, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101012, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101013, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101014, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101015, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101016, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101017, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101018, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101019, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101020, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' },
  { sno: 500101021, sna: 'YouBike2.0_捷運科技大樓站', sarea: '大安區', ar: '復興南路二段235號前' }
])
// 暫用響應參考
//const dataRef = ref(dataArray)
// 過濾顯示資料(換頁)
const dataFiltered = computed(() =>
  dataArray.value.slice(pageCurrent.value * 10, pageCurrent.value * 10 + 10)
)

// 頁面 pages
const pageCurrent = ref(0)
let pageMax = Math.ceil(dataArray.value.length / 10)

function btnPagePrev() {
  if (pageCurrent.value <= 0) {
    return
  }
  pageCurrent.value--
}
function btnPageNext() {
  if (pageCurrent.value >= pageMax) {
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
      <table class="table">
        <thead>
          <tr>
            <th>站點編號</th>
            <th>站點名稱</th>
            <th>站點所在區域</th>
            <th>站點地址</th>
            <!-- <th>總車位數量</th>
            <th>可租借的腳踏車數量</th>
            <th>站點緯度</th>
            <th>站點經度</th>
            <th>可歸還的腳踏車數量</th> -->
          </tr>
        </thead>
        <tbody>
          <tr v-for="data in dataFiltered" v-bind:key="data.sno">
            <td>{{ data.sno }}</td>
            <td>{{ data.sna }}</td>
            <td>{{ data.sarea }}</td>
            <td>{{ data.ar }}</td>
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
