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
})

const features = ref([
  { id: "range", desc: "Range (WLTP)" },
  { id: "acc", desc: "0-100 km/h" },
  { id: "speed", desc: "Top speed" },
  { id: "battery", desc: "Battery" }])


</script>

<template>
  <div class="wrapper">
    <div v-for="item in features" :key="item.id" class="footer-item">
      <div class="icon">
        <AccelerationIco v-if="item.id === 'acc'" />
        <IconBattery v-else-if="item.id === 'battery'" />
        <IconRange v-else-if="item.id === 'range'" />
        <IconSpeed v-else-if="item.id === 'speed'" />
      </div>
      <div class="item-id"></div>
      <div class="label">{{ item.desc }}</div>
      <div class="value">
        <template v-if="item.id === 'range'">{{ range }} km</template>
        <template v-else-if="item.id === 'acc'">{{ acc }} s<br><span>lower wins</span></template>
        <template v-else-if="item.id === 'speed'">{{ speed }} km/h</template>
        <template v-else-if="item.id === 'battery'">{{ battery }} kWh</template>
      </div>
    </div>
  </div>
</template>
<style scoped>
.wrapper {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.footer-item {
  text-align: center;
  border: 1px solid #2C3540;
  border-radius: 10px;
  flex: 0 1 32%;
  font-size: 12px;
  padding: 10px 5px;
}

.item-id {
  color: var(--color-highlight);
}

.label {
  color: #F2F5F8;
}

.value span {
  color: #FFC94D;
}
</style>