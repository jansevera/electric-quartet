<script setup>
import { ref } from 'vue'

defineProps({
  range: {
    type: String,
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


const quartet = ref([
  { id: "A2", desc: "BMW i4" },
  { id: "A3", desc: "Hyundai Ioniq 6" },
  { id: "A4", desc: "Polestar 2" }])

</script>

<template>
  <div class="wrapper">
    <div v-for="item in quartet" :key="item.id" class="quartet-item">
      <div class="item-id">{{ item.id }}</div>
      <div class="label">{{ item.desc }}</div>

    </div>
  </div>
</template>
<style scoped>
.wrapper {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.quartet-item {
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
</style>