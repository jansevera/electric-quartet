<script setup>
import AccelerationIco from './icons/IconAcceleration.vue'
import IconBattery from './icons/IconBattery.vue';
import IconRange from './icons/IconRange.vue';
import IconSpeed from './icons/IconSpeed.vue';
import { ref } from 'vue'

defineProps({
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
  disabled: {
    type: Boolean,
    default: false
  },
})

const emit = defineEmits(['pick'])

const features = ref([
  { id: "range", desc: "Range (WLTP)" },
  { id: "acc", desc: "0-100 km/h" },
  { id: "speed", desc: "Top speed" },
  { id: "battery", desc: "Battery" }])

const pickedClass = "picked"

</script>

<template>
  <div class="wrapper">
    <button v-for="item in features" :key="item.id" class="footer-item" :data-id="item.id"
      :class="{ [pickedClass]: picked === item.id }" :disabled="!!picked || disabled" @click="emit('pick', item.id)">
      <div>
        <div class="icon">
          <AccelerationIco v-if="item.id === 'acc'" />
          <IconBattery v-else-if="item.id === 'battery'" />
          <IconRange v-else-if="item.id === 'range'" />
          <IconSpeed v-else-if="item.id === 'speed'" />
        </div>
        <div class="label">{{ item.desc }}</div>
      </div>
      <div class="value">
        <template v-if="item.id === 'range'">{{ range }} km</template>
        <template v-else-if="item.id === 'acc'">{{ acc }} s<br><span>lower wins</span></template>
        <template v-else-if="item.id === 'speed'">{{ speed }} km/h</template>
        <template v-else-if="item.id === 'battery'">{{ battery }} kWh</template>
      </div>
    </button>
  </div>
</template>
<style lang="scss" scoped>
.wrapper {
  display: flex;
  flex-direction: row;
  justify-content: center;
  gap: 10px;
}

.footer-item {
  text-align: center;
  border: 1px solid #2C3540;
  border-radius: 10px;
  flex: 0 1 32%;
  font-size: 12px;
  padding: 10px 15px;
  max-width: 150px;
  text-align: left;
  background: transparent;
  color: var(--color-text);
  font-family: inherit;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  flex-wrap: wrap;
  cursor: pointer;

  &:hover:not(:disabled) {
    border-color: var(--color-highlight);
    box-shadow: 0 0 14px -4px var(--color-highlight)
  }

  &:disabled {
    opacity: 0.4;
  }

  &.picked {
    opacity: 1;
    border-color: var(--color-highlight);
    background:
      color-mix(in srgb, var(--color-highlight) 12%, var(--color-card-background));
  }
}

.icon {
  display: inline-block;
  width: 23px;
  height: 16px;
}

.item-id {
  color: var(--color-highlight);
}

.label {
  display: inline;
  color: var(--color-muted);
  width: calc(100% - 25px);
}

.value {
  font-size: 17px;
  margin: 5px 0;

  span {
    font-size: 12px;
    line-height: normal;
    color: #FFC94D;
  }
}
</style>