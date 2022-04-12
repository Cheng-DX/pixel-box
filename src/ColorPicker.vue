<script setup lang="ts">
defineProps<{
  modelValue: string
}>()
defineEmits(['update:modelValue'])
const colors = [
  '#006778',
  '#0093AB',
  '#00AFC1',
  '#FFD124',
  '#F90716',
  '#FF5403',
  '#FFCA03',
  '#FFF323',
  '#fff',
  '#000',
  ''
]
</script>

<template>
  <div class="color-picker-root">
    <div class="box"></div>
    <div class="select-panel">
      <div
        v-for="color in colors"
        @click="$emit('update:modelValue', color)"
        :style="{
          backgroundColor: color,
          border: color === '' ? '1px dotted #ffffff66' : ''
        }"
        class="color-box"
      >
        {{ color === '' ? '✏️' : '' }}
      </div>
    </div>
  </div>
</template>

<style scoped>
.color-picker-root {
  display: flex;
  flex-direction: row;
  height: 100px;
}
.box {
  height: 70px;
  width: 70px;
  background-color: v-bind(modelValue);
  border: 1px solid #ffffff1f;
  margin-block: auto;
}
.select-panel {
  display: flex;
  flex-wrap: wrap;
  flex-direction: row;
  justify-content: center;
  align-items: center;
  margin-left: 5px;
  max-width: 300px;
}
.color-box {
  height: 30px;
  width: 30px;
  margin: 3px;
  color: rgba(255, 255, 255, 0.456);
  font-size: 20px;
  text-align: center;
  line-height: 30px;
  cursor: pointer;
}
</style>
