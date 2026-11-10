<script setup>
import { toDisplayString } from 'vue';
import Footer from './components/Footer.vue';
import Header from './components/Header.vue'
import MainSection from './components/MainSection.vue'

const props = defineProps({
  car: {
    type: Object,
    required: true,
  },
  hide: {
    type: Boolean,
    required: false
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

const FALLBACK_IMAGE = '/images/default-image.svg'

function useFallback(event) {
  const img = event.target
  if (!img.src.endsWith(FALLBACK_IMAGE)) {
    img.src = FALLBACK_IMAGE
  }
}
</script>

<template>
  <div class="card">
    <header>
      <Header :car_name="car.car_name" :car_version="car.car_version" :car_id="car.car_id" />
    </header>
    <main>
      <div class="image-wrap">
        <img :alt="car.car_name" class="car-image" :src="`/images/${car.car_id}.png`" width="125" height="125"
          @error="useFallback" />
      </div>
      <div class="car-reflection" aria-hidden="true">
        <img alt="" :src="`/images/${car.car_id}.png`" width="125" height="125" @error="useFallback" />
      </div>
      <MainSection :range="car.range" :acc="car.acc" :speed="car.speed" :battery="car.battery" :picked="props.picked"
        :compare="props.compare" :player="props.player" />
    </main>
    <footer v-if="!hide">
      <Footer />
    </footer>
  </div>
</template>

<style scoped>
.card {
  background-color: var(--color-card-background);
  color: var(--color-card-text);
  padding: 18px;
  border-radius: 18px;
  max-width: 360px;
}

.car-image {
  aspect-ratio: 324 / 140;
  width: 100%;
  height: 100%;
  object-fit: contain;
  object-position: center bottom;
  position: relative;
  z-index: 1;
  /*border: 1px solid red;*/
}

.image-wrap {
  position: relative;
  margin: 0;
}

/* light reflection on the "floor" under the car */
.image-wrap::after {
  content: "";
  position: absolute;
  left: 10%;
  bottom: -12px;
  width: 80%;
  height: 32px;
  background: radial-gradient(ellipse at center,
      color-mix(in srgb, var(--color-highlight) 55%, transparent) 0%,
      color-mix(in srgb, var(--color-highlight) 20%, transparent) 50%,
      transparent 80%);
  filter: blur(6px);
  pointer-events: none;
}

/* mirrored car below the image, fading out */
.car-reflection {
  height: 40px;
  overflow: hidden;
  pointer-events: none;
  /*border: 1px solid green;*/
  margin-top: -9px;
}

.car-reflection img {
  display: block;
  aspect-ratio: 324 / 140;
  width: 100%;
  height: auto;
  object-fit: contain;
  object-position: center bottom;
  transform: scaleY(-1);
  opacity: 0.35;
  -webkit-mask-image: linear-gradient(to top, rgba(0, 0, 0, 0.8) 0%, transparent 30%);
  mask-image: linear-gradient(to top, rgba(0, 0, 0, 0.8) 0%, transparent 30%);

}


header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

footer {
  padding-top: 4rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
