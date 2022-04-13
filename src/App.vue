<script setup lang="ts">
import { TO_DISPLAY_STRING } from '@vue/compiler-core'
import { computed } from '@vue/reactivity'
import { nextTick, ref, watch } from 'vue'
import ColorPicker from './ColorPicker.vue'

const modes = [
  {
    name: '50❌50',
    size: 50
  },
  {
    name: 'huge',
    size: 40
  },
  {
    name: 'large',
    size: 30
  },
  {
    name: 'medium',
    size: 20
  },
  {
    name: 'small',
    size: 10
  },
  {
    name: 'tiny',
    size: 5
  }
]
const mode = ref('medium')
const size = computed(() => {
  const currentMode = modes.find(m => m.name === mode.value)
  return currentMode?.size ? currentMode.size : 0
})
watch(mode, () => {
  pixels.value = Array.from({ length: size.value }, () =>
    Array.from({ length: size.value }, () => '')
  )
})
function switchMode() {
  const currentMode = modes.find(m => m.name === mode.value)
  if (currentMode) {
    const index = modes.indexOf(currentMode)
    const nextIndex = (index + 1) % modes.length
    mode.value = modes[nextIndex].name
  }
}
const color = ref('')

const pixels = ref(
  Array.from({ length: size.value }, () =>
    Array.from({ length: size.value }, () => '')
  )
)
function clickBox(e: any) {
  const target = e.target as HTMLDivElement
  const row = Number(target.getAttribute('data-row'))
  const col = Number(target.getAttribute('data-col'))
  if (row !== undefined && col !== undefined) {
    pixels.value[row][col] = color.value
  }
}
function exportJSON() {
  const result = []
  for (let i = 0; i < size.value; i++) {
    for (let j = 0; j < size.value; j++) {
      if (pixels.value[i][j]) {
        result.push({
          x: i,
          y: j,
          color: pixels.value[i][j]
        })
      }
    }
  }
  const json = {
    mode: mode.value,
    data: result
  }
  const a = document.createElement('a')
  a.href =
    'data:text/json;charset=utf-8,' + encodeURIComponent(JSON.stringify(json))
  a.download = 'pixels.json'
  a.click()
}
async function handleFile(event: any) {
  const file = event.target.files[0]
  const { mode: savedMode, data } = JSON.parse(await file.text())
  if (data.length === 0) {
    alert('EMPTY FILE')
    return
  }
  mode.value = savedMode
  _clear()
  nextTick(() => {
    for (let item of data) {
      pixels.value[item.x][item.y] = item.color
    }
  })
}

function _clear() {
  pixels.value = Array.from({ length: size.value }, () =>
    Array.from({ length: size.value }, () => '')
  )
}
</script>

<template>
  <div class="root">
    <div class="tools">
      <button @click="switchMode" class="export-button">
        {{ mode.toUpperCase() }}
      </button>
      <color-picker v-model="color" />
      <div class="button-group">
        <label for="uploadFile" class="label">
          <button class="export-button">IMPORT</button>
          <input
            type="file"
            @change="handleFile"
            name="upload"
            accept=".json"
            class="upload-file"
          />
        </label>
        <button @click="exportJSON" class="export-button">EXPORT</button>
      </div>
    </div>

    <div @click="clickBox">
      <div v-for="(row, idx1) in pixels" class="row">
        <div
          v-for="(item, idx2) in row"
          class="pixel"
          :style="{
            backgroundColor: item
          }"
          :data-row="idx1"
          :data-col="idx2"
          :key="`${idx1}-${idx2}`"
        ></div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.root {
  display: flex;
  flex-direction: column;
  align-items: center;
  min-width: 670px;
}
.tools {
  display: flex;
  width: 670px;
  height: 100px;
  flex-direction: row;
  justify-content: space-around;
  align-items: center;
}
.button-group {
  display: flex;
  height: 100%;
  flex-direction: column;
  align-items: center;
  justify-content: space-evenly;
}
.export-button {
  height: 37px;
  width: 100px;
  border: 1px solid #ffffff1f;
  border-radius: 5px;
  background-color: #ffffff1f;
  color: #ffffff;
  cursor: pointer;
  font-weight: 700;
}
.label {
  position: relative;
}
.upload-file {
  position: absolute;
  left: 0;
  top: 0;
  opacity: 0;
  cursor: pointer;
  height: 37px;
  width: 100px;
}
.row {
  display: flex;
  flex-direction: row;
}
.pixel {
  width: v-bind(600 / size + 'px');
  height: v-bind(600 / size + 'px');
  border: 1px dotted #ffffff1f;
}
</style>
