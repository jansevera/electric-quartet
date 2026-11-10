<script setup>
import { ref, computed } from 'vue'
const props = defineProps({
  userCards: {
    type: Number,
    required: true,
  },

  rivalCards: {
    type: Number,
    required: true,
  }
})
const maxCards = 32

const rivalCards = computed(() => maxCards - props.userCards)


const userName = ref("Jan")
const userAbr = computed(() => {
  const parts = userName.value.trim().split(" ").filter(Boolean)
  if (parts.length === 0) return "I"
  if (parts.length === 1) return parts[0].slice(0, 1).toLocaleUpperCase()
  return (parts[0][0] + parts[parts.length - 1][0]).toLocaleUpperCase()
})

const rivalName = ref("Rival")
const rivalAbr = ref("R")



const barWidth = computed(() => {
  const ratio = props.userCards / maxCards
  return Math.min(Math.max(ratio, 0), 1) * 100 + '%'
})

</script>

<template>
  <div class="top-bar">
    <div class="profile">
      <div class="icon">{{ userAbr }}</div>
      <div><strong>{{ userName }}</strong><br /><span class="muted">your pick</span></div>
    </div>
    <div class="score">
      <div class="text">
        <div><strong>{{ userCards }}</strong> <span class="muted">cards</span></div>
        <div><span class="muted">of 32</span></div>
        <div>{{ rivalCards }} <span class="muted">cards</span></div>
      </div>
      <div class="score-bar">
        <div class="bar" :style="{ width: barWidth }">&nbsp;</div>
      </div>
    </div>
    <div class="profile rival">
      <div class="icon">{{ rivalAbr }}</div>
      <div><strong>{{ rivalName }}</strong><br /><span class="muted">CPU · round 12</span></div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.top-bar {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  gap: 10px;

  .profile {
    width: 20%;
    display: flex;
    gap: 10px;
    align-items: center;
    line-height: 1.1em;

    span {
      font-size: 12px;
    }

    &.rival {
      flex-direction: row-reverse;
      text-align: right;

      .icon {
        border-color: var(--rival);
        color: var(--rival)
      }
    }
  }
}

.icon {
  display: inline-block;
  width: 38px;
  height: 38px;
  padding: 5px;
  border: 2px solid var(--color-highlight);
  border-radius: 50%;
  color: var(--color-highlight);
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: 700;
  font-size: 14px;
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

.score {
  max-width: 340px;
  width: 60%;

  .text {
    width: 100%;
    display: flex;
    flex-direction: row;
    justify-content: space-between;
  }
}

.score-bar {
  background-color: var(--rival);
  height: 6px;
  width: 100%;
  max-width: 340px;
  border-radius: 3px;

  .bar {
    background-color: #3DF5C8;
    border-radius: 3px;
    height: 6px;
  }
}
</style>