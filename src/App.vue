<script setup>
import { computed, onMounted, reactive, ref } from "vue";
const pAtmInput = ref(96)
const m = 16.5 * Math.pow(10, -3);
const V = 11.5 * Math.pow(10, -3);
const S = 2 * Math.pow(10, -4)
const eps = ref(0.0);
const N = ref(3.0);
// const cache = computed(() => ( / )
const data = reactive([]);

const mK = ref(0.0);
const mDK = ref(0.0);

function subtractN() {
  data.pop()
  N.value--
}
function addN() {
  data.push({ n: 0, ts: 0 })
  N.value++
}
function fillTable() {
  data.splice(0)
  data.push({ n: 5, ts: 5.7 });
  data.push({ n: 4, ts: 4.6 });
  data.push({ n: 3, ts: 3.4 });
  // for (let i = 0; i < N.value; i++) {

  // }
}
function clear() {
  mK.value = 0.0
  mDK.value = 0.0

}
onMounted(() => {
  clear()
  fillTable()
});


function calculate() {
  clear()
  let tk = 0.0;
  let dk = 0.0;
  data.forEach((x) => {
    x.Ts = x.ts / x.n;
    x.k = (4 * Math.pow(Math.PI, 2) * m * V) / (Math.pow(S, 2) * pAtmInput.value * Math.pow(10, 3) * Math.pow(x.Ts, 2))
    tk += x.k
  })
  tk /= data.length
  console.log("mK", tk)

  data.forEach((x) => {
    x.dk = tk - x.k
    console.log("dk", x.dk);
    dk += x.dk
  })
  console.log('mDK', mDK.value);
  dk /= data.length;

  mK.value = tk;
  mDK.value = dk;
  eps.value = (dk / tk) * 100;
}
function filter(e, { toFixed, toExpo }) {
  if (!e && e !== 0) {
    // console.log('ESHKERE ', e);
    return '0'
  }
  if (toFixed) {
    return parseFloat(e).toFixed(toFixed)
  }
  if (toExpo) {
    return e.toExponential(toExpo)
  }
  return e

}
</script>

<template>
  <div class="w-full min-h-screen flex justify-center items-center">

    <div class="flex flex-col">
      <div class="flex">
        <div class="w-1/2 flex flex-col space-y-1 mb-4">
          <h1>P - атмосферное давление:</h1>
          <div class="flex items-center space-x-2"><input type="number" name="number" v-model="pAtmInput"
              class="w-8 text-center p-1 border-black bg-light-900 border-2" />
            <h1>10^3 Pa</h1>
          </div>
        </div>
        <div class="w-1/2 justify-end flex space-x-1 h-10 items-center">
          <button v-if="N > 3" class="rounded-md border px-1.5" @click="subtractN">-</button>
          <h1>N: {{ N }}</h1>
          <button v-if="N < 10" class="rounded-md border px-1.5 " @click="addN">+</button>

        </div>
      </div>
      <table class="border-collapse bg-gray-50/50">
        <thead>
          <tr class="itemsHeader">
            <th>№</th>
            <th>n</th>
            <th>t(s)</th>
            <th>T(s)</th>
            <th>k</th>
            <th>Δk</th>
            <th>S%</th>
          </tr>
        </thead>
        <tbody>
          <tr class="items" v-for="(d, index) in data" :key="index">
            <td class="text-center font-bold">{{ index + 1 }}</td>
            <td>
              <input type="number" name="number" v-model="d.n"
                class="w-full text-center p-1 border-black bg-light-900 border-2" />
            </td>
            <td>
              <input type="number" name="number" v-model="d.ts"
                class="w-full text-center p-1 border-black bg-light-900 border-2" />
            </td>
            <td>{{ filter(d.Ts, { toFixed: 2 }) }}</td>
            <td>{{ filter(d.k, { toFixed: 2 }) }}</td>
            <td>{{ filter(d.dk, { toFixed: 3 }) }}</td>
            <td class="text-xs !px-1" v-if="index === 0" :rowspan="data.length + 1">{{ filter(eps, { toExpo: 2 }) }}%</td>

          </tr>
          <tr rowspan="3">
            <td>
              <h4 class="text-xs">Среднее <br> Значение</h4>
            </td>
            <td colspan="3">
            </td>
            <td>{{ filter(mK, { toFixed: 3 }) }}</td>
            <td>{{ filter(mDK, { toExpo: 1 }) }}</td>
          </tr>
        </tbody>
      </table>
      <div class="flex w-full justify-end mt-4">
        <button class="rounded-md border py-1 px-2" @click="calculate">Вычислять</button>

      </div>
    </div>
  </div>
</template>

<style scoped>
tr th {
  @apply border p-1;
}

tr td {
  @apply border p-2 px-3 text-center w-20;

}

.itemsHeader th {
  min-width: 52px;
}
</style>
