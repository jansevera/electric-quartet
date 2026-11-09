<script setup>
import CardFront from './CardFront.vue';
import cars from '@/data/cars.json'
import CardBack from './CardBack.vue';
import { ref, computed } from 'vue'
import GameTableFooter from './components/GameTableFooter.vue';

const selectedId = ref('A1')
const car = computed(() => cars.find(c => c.car_id === selectedId.value))

</script>

<template>

    <div class="game-table">
        <div class="top-bar">
            <div class="profile">Jan</div>
            <div class="score-bar">---</div>
            <div class="profile">Rival</div>
        </div>
        <!-- You -->
        <div class="pile">
            <div>Your deck</div>
            <div class="deck-pile">
                <CardBack />
                <CardBack />
                <CardBack />
            </div>
            <div>XX cards</div>
        </div>
        <div class="player-zone">
            <div class="play-card">
                <CardFront v-if="car" :car="car" hide /><!-- hide footer? -->
            </div>
        </div>
        <div class="duel-center">VS
            Rival card stays face down until you pick</div>
        <div class="player-zone rival"><!-- Rival -->
            <div class="play-card">
                <CardBack />
            </div>
        </div>
        <div class="pile">Rival deck
            <div class="deck-pile">
                <CardBack />
                <CardBack />
                <CardBack />
            </div>
            <div>XX cards</div>
        </div>

        <div class="stat-picker"><strong>Your turn.</strong> Pick the stat you think beats the hidden card.
            <GameTableFooter v-if="car" :range="car.range" :acc="car.acc" :speed="car.speed"
                :battery="car.battery" />
        </div>

    </div>
    <!--<Card v-for="car in cars" :key="car.car_id" :car="car" />-->


</template>

<style scoped>
.deck-pile {
    --s: .34;
}

.player-zone.rival {
    @media (max-width: 768px) {
        font-size: calc(14px * 0.7);

        .back {
            width: 100%;
        }
    }



}
</style>
