<script setup>
import Footer from './components/Footer.vue';
import Header from './components/Header.vue'
import MainSection from './components/MainSection.vue'
import DefaultImage from '/images/default-image.svg'
import { ref } from 'vue';

const cars = ref(
  [
    {
      "car_number": "A1",
      "car_name": "Tesla Model 3",
      "car_version": "Long Range AWD",
      "range": 629,
      "acc": 4.4,
      "speed": 201,
      "battery": 78,
    },

  ])
</script>

<template>
  <div class="card">
    <header>
      <Header :car_name=cars[0].car_name :car_version=cars[0].car_version :car_number=cars[0].car_number />
    </header>
    <main>
      <div class="image-wrap">
        <img :alt="cars[0].car_name" class="car-image" :src="`/images/${cars[0].car_number}.png`" width="125"
          height="125" />
      </div>
      <div class="car-reflection" aria-hidden="true">
        <img alt="" :src="`/images/${cars[0].car_number}.png`" width="125" height="125" />
      </div>
      <MainSection :range="cars[0].range" :acc="cars[0].acc" :speed="cars[0].speed" :battery="cars[0].battery" />
    </main>
    <footer>
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
