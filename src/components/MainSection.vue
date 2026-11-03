<script setup>
import FeatureItem from './FeatureItem.vue'
import AccelerationIco from './icons/IconAcceleration.vue'
import IconBattery from './icons/IconBattery.vue';
import IconRange from './icons/IconRange.vue';
import IconSpeed from './icons/IconSpeed.vue';
import { ref } from 'vue'
import maxValues from '@/data/maxValues.json'

const props = defineProps({
  range: {
    type: Number,
    required: true,
  },
  acc: {
    type: Number,
    required: true,
  },
  speed: {
    type: Number,
    required: true,
  },
  battery: {
    type: Number,
    required: true,
  },
})


const features = ref([
  { id: "range", desc: "Range (WLTP)" },
  { id: "acc", desc: "0-100 km/h" },
  { id: "speed", desc: "Top speed" },
  { id: "battery", desc: "Battery" }])

function barWidth(id) {
  const value = props[id]
  const maxValue = maxValues[id]
  let ratio
  if (id == 'acc') {
    ratio = maxValue / value
  }
  else {
    ratio = value / maxValue
  }
  return Math.min(Math.max(ratio, 0), 1) * 100 + '%'
}

</script>

<template>
  <FeatureItem v-for="item in features" :key="item.id">
    <template #icon>
      <AccelerationIco v-if="item.id == 'acc'" />
      <IconBattery v-if="item.id == 'battery'" />
      <IconRange v-if="item.id == 'range'" />
      <IconSpeed v-if="item.id == 'speed'" />
    </template>
    <template #label>{{ item.desc }}</template>
    <template #value>
      <div v-if="item.id == 'range'">{{ range }} km</div>
      <div v-if="item.id == 'acc'">{{ acc }} s</div>
      <div v-if="item.id == 'speed'">{{ speed }} km/h</div>
      <div v-if="item.id == 'battery'">{{ battery }} kWh</div>
    </template>
    <template #bar>
      <div class="bar" :style="{ width: barWidth(item.id) }">&nbsp;</div>
    </template>

  </FeatureItem>

</template>
