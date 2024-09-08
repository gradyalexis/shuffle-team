<script setup>
import { ref, inject, computed } from 'vue'

const isLoading = ref(true)
const axios = inject('axios')
const originalData = ref('https://opensheet.elk.sh/1HRpOj0-O6PuUuU9DC5FLR0KUVzBwhFcC7r-N7FZaV8Q/1')
const fakeData = ref('http://localhost:5173/dummy.json');

const data = ref([])
const divider = ref(4)

const getList = () => {
  axios.get(originalData.value).then((response) => {
    data.value = response.data
  }).finally(() => {
    isLoading.value = false
  });
}

const divideData = computed(() => {
  return data.value.reduce((acc, current, index) => {
    const chunkIndex = Math.floor(index / divider.value)
    if (!acc[chunkIndex]) {
      acc[chunkIndex] = []
    }
    acc[chunkIndex].push(current)
    return acc
  }, [])
})

const shuffleData = () => {
  data.value = data.value.sort(() => Math.random() - 0.5)
}

getList()
</script>

<template>
  <div class="flex mb-10 gap-4">
    <button class="border border-white px-4 py-3 rounded ml-auto" @click="shuffleData">Shuffle</button>
    <div class="flex">
      <button class="border border-white py-3 px-5 rounded" @click="divider--">-</button>
      <input type="text" class="border border-white p-3 rounded bg-transparent text-center" v-model="divider" readonly />
      <button class="border border-white py-3 px-5 rounded" @click="divider++">+</button>
    </div>
  </div>
  <div class="grid grid-cols-3 text-xl gap-10">
    <div v-for="team in divideData" class="border border-white p-4 rounded-md">
      <div class="grid grid-cols-2 gap-2 !font-bold border-b border-white mb-2 pb-2">
        <div>Job/Class</div>
        <div>Nickname</div>
      </div>
      <div v-for="player in team" class="grid grid-cols-2 gap-2">
        <div>{{ player['Job/Class'] }}</div>
        <div>{{ player.Nickname }}</div>
        <!-- <div>{{ player['Discord Username (example: kemzkun)'] }}</div> -->
      </div>
    </div>
  </div>
</template>

<style scoped>

</style>
