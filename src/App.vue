<template>
  <TimeFrequencyGraph @timeSelect="handleTimeSelect"></TimeFrequencyGraph>
  <Spectrogram ref="spectrogramRef"></Spectrogram>
</template>

<script setup lang="ts">
import TimeFrequencyGraph from "./components/TimeFrequencyGraph.vue";
import Spectrogram from "./components/Spectrogram.vue";
import { onMounted, ref } from "vue";
const spectrogramRef = ref()
const handleTimeSelect = (val: any) => {
  spectrogramRef.value.draw(val)
};
onMounted(()=>{
  console.log();
})
</script>

<style scoped lang="less"></style>
