<script setup lang="ts">
import colors from './assets/colors.json'

defineProps<{
  modelValue: string
}>()
defineEmits(['update:modelValue'])
</script>

<template>
  <div class="color-picker-root">
    <div class="current-color">
      <div class="box"></div>
      <span class="color-text">{{ modelValue }}</span>
    </div>
    <div class="select-panel">
      <div
        v-for="color in colors"
        @click="$emit('update:modelValue', color)"
        :style="{
          backgroundColor: color
        }"
        class="color-box"
      >
        {{ color === '' ? '❌' : '' }}
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
.current-color {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
}
.box {
  height: 60px;
  width: 60px;
  background-color: v-bind(modelValue);
  border: 1px solid #ffffff1f;
  border-radius: 20px;
  transition: 0.2s;
}
.color-text {
  color: v-bind(modelValue);
  font-family: consolas;
  font-size: 15px;
  font-weight: bold;
  margin-top: 2px;
}
.select-panel {
  display: flex;
  flex-wrap: wrap;
  flex-direction: row;
  justify-content: flex-start;
  align-items: center;
  align-content: center;
  margin-left: 5px;
  max-width: 360px;
}
.color-box {
  height: 20px;
  width: 20px;
  margin: 1px;
  color: #fff;
  border-radius: 5px;
  font-size: 10px;
  text-align: center;
  line-height: 20px;
  cursor: pointer;
}
</style>
