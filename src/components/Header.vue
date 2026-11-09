<script setup>
defineProps({
  car_name: {
    type: String,
    required: true,
  },
  car_version: {
    type: String,
    required: true,
  },
  car_id: {
    type: String,
    required: true,
  },
})

</script>

<template>
  <div class="wrapper">
    <div class="title">
      <div class="quartet">Quartet {{ car_id.charAt(0) }}</div>
      <h1>{{ car_name }}</h1>
      <h2>{{ car_version }}</h2>
    </div>
    <div class="card-number">
      <h3>{{ car_id }}</h3>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.wrapper {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  align-items: center;
}

.quartet {
  color: var(--color-highlight);
  text-transform: uppercase;
  letter-spacing: 2px;
  font-weight: bold;
  font-size: 12px;
  line-height: 1.4;
}

h1 {
  font-weight: 700;
  font-size: 28px;
  line-height: 1.05em;
  letter-spacing: 0;
}

h2 {
  color: #A9B2BE;
  font-size: 13px;
}

h3 {
  font-size: 18px;
  padding: 5px;
  border: 2px solid var(--color-highlight);
  border-radius: 50%;
  width: 46px;
  height: 46px;
  color: var(--color-highlight);
  display: flex;
  justify-content: center;
  align-items: center;
}

.title {

  h1,
  h3 {
    text-align: left;
  }
}

@media (min-width: 1024px) {

  .title h1,
  .title h3 {
    text-align: left;
  }
}
</style>
