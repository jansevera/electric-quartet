<script setup>
import FeatureItem from './FeatureItem.vue'
import AccelerationIco from './icons/IconAcceleration.vue'
import IconBattery from './icons/IconBattery.vue';
import IconRange from './icons/IconRange.vue';
import IconSpeed from './icons/IconSpeed.vue';
import { ref, computed } from 'vue'
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
  picked: {
    type: String,
    required: false
  },
  compare: {
    type: String,
    required: false
  },
  player: {
    type: String,
    required: false
  }
})

const pickedClass = "picked"

const opposite = { win: 'lose', lose: 'win', tie: 'tie' }

// compare is always from the user's point of view, so flip it for the rival
const resultClass = computed(() =>
  props.player === 'user' ? props.compare : opposite[props.compare]
)

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
  <FeatureItem v-for="item in features" :key="item.id"
    :class="picked === item.id ? [pickedClass, resultClass] : picked ? 'dim' : ''">
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

<style lang="scss" scoped>
.dim {
  opacity: .4;
}

.picked {
  border-radius: 5px;

  &.win {
    border-color: var(--color-highlight);
    color: var(--color-highlight);
    box-shadow: 0 0 1.2em -.2em color-mix(in srgb, var(--color-highlight) 60%, transparent);
  }

  &.lose {
    border-color: var(--rival);
    color: var(--rival);
    box-shadow: 0 0 1.2em -.2em color-mix(in srgb, var(--rival) 60%, transparent);

    .bar {
      background-color: var(--rival);
    }
  }

  &.tie {
    border-color: var(--tie);
    color: var(--tie);
    box-shadow: 0 0 1.2em -.2em color-mix(in srgb, var(--tie) 60%, transparent);

    .bar {
      background-color: var(--tie);
    }
  }
}
</style>
