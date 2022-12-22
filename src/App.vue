<script setup>
import { computed, reactive, ref } from "vue";
const m = ref(16.5);
const d = ref(16);
const P = ref(1020);
const V = ref(11.5);
const sP = ref(0)
const S = computed(() => (Math.PI * d.value * d.value) / 4);
const cache = computed(() =>  Math.PI*Math.PI * m.value * V.value / (S.value * S.value * P.value))
const data = reactive([]);
data.push({ n: 0, ts: 0 });
data.push({ n: 0, ts: 0 });
data.push({ n: 0, ts: 0 });
function calculate() {
  let mG = 0;
  let mDG = 0;
  data.forEach((x)=>{
    x.Ts = x.ts / x.n;
    x.gamma = cache.value / (x.Ts*x.Ts)
    mG += x.gamma
  })
  mG = mG / data.length
  
  data.forEach((x)=>{
    x.dgamma = mG - x.gamma
    mDG += x.dgamma
  })
  mDG = mDG / 3
  sP.value = mDG / mG * 100
}
function filter(e, {toFixed, toExpo}){
  if(!e){
    return ''
  }
  if(toFixed){
    return e.toFixed(toFixed)
  }
  if(toExpo){
    return e.toExponential(toExpo)
  }
  return e

}
</script>

<template>
  <div class="w-full min-h-screen flex justify-center items-center">
   <div class="flex flex-col">
    <table class="border-collapse bg-gray-50/50">
      <thead>
        <tr class="itemsHeader">
          <th>№</th>
          <th>n</th>
          <th>t(s)</th>
          <th>T(s)</th>
          <th>γ</th>
          <th>Δγ</th>
          <th>S%</th>
        </tr>
      </thead>
      <tbody>
        <tr class="items" v-for="(d, index) in data" :key="index">
          <td class="text-center font-bold">{{ index + 1 }}</td>
          <td>
            <input
              type="number"
              name="number"
              v-model="d.n"
              class="w-8 text-center p-1 border-black bg-light-900 border-b-2"
            />
          </td>
          <td >
            <input
              type="number"
              name="number"
              v-model="d.ts"
              class="w-12 text-center p-1 border-black bg-light-900 border-b-2"
            />
          </td>
          <td>{{ filter(d.Ts,{toFixed: 2}) }}</td>
          <td>{{ filter(d.gamma, {toExpo : 3}) }}</td>
          <td>{{ filter(d.dgamma, {toExpo : 3}) }}</td>
        
        </tr>
        <td>{{ d.s }}</td>
      </tbody>
    </table>
    <div class="flex w-full justify-end mt-4">
    <button class="rounded-md border p-2" @click="calculate">Calculate</button>

    </div>
  </div>
  </div>
</template>

<style scoped>
tr th {
  @apply border p-1;
}

tr td {
  @apply border p-2 text-center;
  
}

.itemsHeader th {
  min-width: 52px;
}
</style>
