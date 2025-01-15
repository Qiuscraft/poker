<template>
  <div>
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
    
    <div class="config">
      <div>模板：</div>
      <el-select
        v-model="blind"
        placeholder="请选择模板"
        style="width: 240px"
        @change="selectBlind"
      >
        <el-option
          v-for="item in blind_options"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        />
      </el-select>

      <div>涨盲时间：（单位：分钟）</div>
      <el-input-number v-model="rising_time" :min="1" :max="1440" />

      <div>小盲列表：（大盲会自动计算）</div>
      <el-input
        v-model="sb_list"
        style="width: 240px"
        autosize
        type="textarea"
      />

    </div>
  </div>
</template>

<script setup lang="ts">
import { templates } from '~/composables/templates';

const started = ref(false)
const paused = ref(false)
const time = ref(0)
let id: NodeJS.Timeout
const blind = ref('default')
const template = ref(templates[blind.value])
const rising_time = ref(template.value.rising_time)
const sb_list = ref(template.value.sb_list)
const blind_options = Object.keys(templates).map(key => ({
  value: key,
  label: key,
}))

function selectBlind() {
  if (template.value) {
    rising_time.value = template.value.rising_time;
    sb_list.value = template.value.sb_list;
  }
}

const blindLevels = computed(() => {
  return sb_list.value.split('\n').map((sb) => {
    return {
      smallBlind: parseInt(sb),
      bigBlind: parseInt(sb) * 2
    }
  })
})

const currentLevel = computed(() => {
  return Math.floor(time.value / (rising_time.value * 60))
})

const smallBlind = computed(() => {
  if (currentLevel.value >= blindLevels.value.length) 
    return blindLevels.value[blindLevels.value.length - 1].smallBlind
  return blindLevels.value[currentLevel.value].smallBlind
})

const bigBlind = computed(() => {
  if (currentLevel.value >= blindLevels.value.length) 
    return blindLevels.value[blindLevels.value.length - 1].bigBlind
  return blindLevels.value[currentLevel.value].bigBlind
})

const progressPercentage = computed(() => {
  return (time.value % (rising_time.value * 60)) / (rising_time.value * 60) * 100
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
    time.value += 0.01
  }, 10)
}

function stop() {
  started.value = false
  paused.value = false
  clearInterval(id)
}

const timeString = computed(() => {
  const hours = Math.floor(time.value / 3600)
  const minutes = Math.floor((time.value % 3600) / 60)
  const seconds = Math.floor(time.value % 60)
  const hours_str = String(hours).padStart(2, '0')
  const minutes_str = String(minutes).padStart(2, '0')
  const seconds_str = String(seconds).padStart(2, '0')
  return `${hours_str}:${minutes_str}:${seconds_str}`
})

useHead({
  title: '计时器'
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

  .config {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100vh;
    text-align: center;
  }
</style>
