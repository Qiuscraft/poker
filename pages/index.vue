<template>
  <div class="timer-container">
    <div class="time-display">{{ timeString }}</div>
    <div class="blind-display">
      <span>盲注: {{ smallBlind }} / {{ bigBlind }}</span>
    </div>
    <div class="button-group">
      <div v-if="started && !paused">
        <el-button type="primary" plain disabled>开始计时</el-button>
        <el-button type="warning" plain @click="pause()">暂停计时</el-button>
        <el-button type="danger" plain @click="stop()">停止计时</el-button>
      </div>
      <div v-else-if="started && paused">
        <el-button type="primary" plain disabled>开始计时</el-button>
        <el-button type="warning" plain @click="resume()">继续计时</el-button>
        <el-button type="danger" plain @click="stop()">停止计时</el-button>
      </div>
      <div v-else>
        <el-button type="primary" plain @click="start()">开始计时</el-button>
        <el-button type="warning" plain disabled>暂停计时</el-button>
        <el-button type="danger" plain disabled>停止计时</el-button>
      </div>
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
  return Math.floor(time.value / 36000)
})

const smallBlind = computed(() => {
  return blindLevels[currentLevel.value].smallBlind
})

const bigBlind = computed(() => {
  return blindLevels[currentLevel.value].bigBlind
})

function start() {
  started.value = true
  paused.value = false
  time.value = 0
  id = setInterval(() => {
    time.value += 10
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
  let totalMilliseconds = time.value
  let hours = Math.floor(totalMilliseconds / 3600000)
  let minutes = Math.floor((totalMilliseconds % 3600000) / 60000)
  let seconds = Math.floor((totalMilliseconds % 60000) / 1000)
  let milliseconds = Math.floor((totalMilliseconds % 1000) / 10)
  let hours_str = String(hours).padStart(2, '0')
  let minutes_str = String(minutes).padStart(2, '0')
  let seconds_str = String(seconds).padStart(2, '0')
  let milliseconds_str = String(milliseconds).padStart(2, '0')
  return `${hours_str}:${minutes_str}:${seconds_str}.${milliseconds_str}`
})
</script>

<style scoped>
.timer-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  font-family: Arial, sans-serif;
}

.time-display {
  font-size: 48px;
  margin-bottom: 20px;
}

.blind-display {
  font-size: 48px;
  margin-bottom: 20px;
}

.button-group {
  display: flex;
  gap: 10px;
}

.el-button {
  font-size: 18px;
  padding: 10px 20px;
}
</style>