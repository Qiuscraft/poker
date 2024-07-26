<template>
  <div class="center-container">
    <div>{{ timeString }}</div>
    <div>盲注: {{ smallBlind }} / {{ bigBlind }}</div>
    <div class="progress-container">
      <el-progress :percentage="progressPercentage" :show-text="false" />
    </div>
    <div v-if="started && !paused" class="button-container">
      <el-button type="primary" plain disabled>开始计时</el-button>
      <el-button type="warning" plain @click="pause()">暂停计时</el-button>
      <el-button type="danger" plain @click="stop()">停止计时</el-button>
    </div>
    <div v-else-if="started && paused" class="button-container">
      <el-button type="primary" plain disabled>开始计时</el-button>
      <el-button type="warning" plain @click="resume()">继续计时</el-button>
      <el-button type="danger" plain @click="stop()">停止计时</el-button>
    </div>
    <div v-else class="button-container">
      <el-button type="primary" plain @click="start()">开始计时</el-button>
      <el-button type="warning" plain disabled>暂停计时</el-button>
      <el-button type="danger" plain disabled>停止计时</el-button>
    </div>
  </div>
</template>

<script setup lang="ts">
const started = ref(false)
const paused = ref(false)
const time = ref(0)
let id: NodeJS.Timeout

const blindLevels = [
  { smallBlind: 10, bigBlind: 20 },
  { smallBlind: 15, bigBlind: 30 },
  { smallBlind: 20, bigBlind: 40 },
  { smallBlind: 25, bigBlind: 50 },
  { smallBlind: 30, bigBlind: 60 },
  { smallBlind: 40, bigBlind: 80 },
  { smallBlind: 50, bigBlind: 100 },
  { smallBlind: 60, bigBlind: 120 },
  { smallBlind: 80, bigBlind: 160 },
  { smallBlind: 100, bigBlind: 200 },
  { smallBlind: 120, bigBlind: 240 },
  { smallBlind: 150, bigBlind: 300 },
  { smallBlind: 200, bigBlind: 400 },
  { smallBlind: 250, bigBlind: 500 },
  { smallBlind: 300, bigBlind: 600 },
  { smallBlind: 400, bigBlind: 800 },
  { smallBlind: 500, bigBlind: 1000 },
  { smallBlind: 600, bigBlind: 1200 }
]

const currentLevel = computed(() => {
  return Math.floor(time.value / 360)
})

const smallBlind = computed(() => {
  return blindLevels[currentLevel.value].smallBlind
})

const bigBlind = computed(() => {
  return blindLevels[currentLevel.value].bigBlind
})

const progressPercentage = computed(() => {
  return (time.value % 360) / 360 * 100
})

function start() {
  started.value = true
  paused.value = false
  time.value = 0
  id = setInterval(() => {
    time.value += 0.01
  }, 10)
}

function pause() {
  paused.value = true
  clearInterval(id)
}

function resume() {
  paused.value = false
  id = setInterval(() => {
    time.value += 10
  }, 10)
}

function stop() {
  started.value = false
  paused.value = false
  clearInterval(id)
}

const timeString = computed(() => {
  let hours = Math.floor(time.value / 3600)
  let minutes = Math.floor((time.value % 3600) / 60)
  let seconds = Math.floor(time.value % 60)
  let hours_str = String(hours).padStart(2, '0')
  let minutes_str = String(minutes).padStart(2, '0')
  let seconds_str = String(seconds).padStart(2, '0')
  return `${hours_str}:${minutes_str}:${seconds_str}`
})
</script>

<style scoped>
.center-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  font-size: 3rem;
  font-weight: bold;
  text-align: center;
}

.progress-container {
  width: 18rem;
}

</style>