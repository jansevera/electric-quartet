<script setup>
import CardFront from './CardFront.vue';
import cars from '@/data/cars.json'
import CardBack from './CardBack.vue';
import { ref, computed } from 'vue'
import GameTableFooter from './components/GameTableFooter.vue';
import GameTableHeader from './components/GameTableHeader.vue';

const selectedId = ref('A1')
const car = computed(() => cars.find(c => c.car_id === selectedId.value))

const oponentId = ref('A2')
const oponentCar = computed(() => cars.find(c => c.car_id === oponentId.value))

const userCards = 5
const maxCards = 32

const rivalCards = computed(() => maxCards - userCards)

const revealed = ref(false)
const pickedStat = ref(null)

const user = "user"
const oponent = "oponent"

function onPick(statId) {
    pickedStat.value = statId
    revealed.value = true
    compareValues(statId)
}

function compareValues(statId) {
    const userValue = car.value[statId]
    const oponentValue = oponentCar.value[statId]
    if (userValue > oponentValue) {
        if (statId === 'acc') { return "lose" }
        else return "win"
    }
    else if (userValue < oponentValue) {
        if (statId === 'acc') { return "win" }
        else return "lose"
    }
    else return "tie"
}
</script>

<template>

    <div class="game-table">
        <GameTableHeader :userCards="userCards" :rivalCards="rivalCards" />
        <!-- You -->
        <div class="pile">
            <div>Your deck</div>
            <div class="deck-pile">
                <CardBack />
                <CardBack />
                <CardBack />
            </div>
            <div><strong>{{ userCards }}</strong> cards</div>
        </div>
        <div class="player-zone">
            <div class="zone-caption text-center uppercase muted">You play</div>

            <div class="play-card">
                <CardFront v-if="car" :car="car" :picked="pickedStat" :compare="compareValues(pickedStat)"
                    :player="user" hide />
                <!-- hide footer? -->
            </div>
        </div>
        <div class="duel-center">
            <h1>VS</h1>
            <div class="muted">Rival card stays face down until you pick</div>
        </div>
        <div class="player-zone rival"><!-- Rival -->
            <div class="zone-caption text-center uppercase muted">Rival plays</div>
            <div class="play-card">
                <CardFront v-if="revealed && oponentCar" :car="oponentCar" :picked="pickedStat"
                    :compare="compareValues(pickedStat)" :player="oponent" hide />
                <!-- hide footer? -->
                <CardBack v-else />
            </div>
        </div>
        <div class="pile">Rival deck
            <div class="deck-pile">
                <CardBack />
                <CardBack />
                <CardBack />
            </div>
            <div><strong>{{ rivalCards }}</strong> cards</div>
        </div>

        <div class="stat-picker">
            <p><strong class="highlight">Your turn.</strong> Pick the stat you think beats the hidden
                card.</p>
            <GameTableFooter v-if="car" :range="car.range" :acc="car.acc" :speed="car.speed" :battery="car.battery"
                @pick="onPick" />
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

.zone-caption {
    font-size: 14px;
    margin-bottom: 10px;
}

.duel-center {
    text-align: center;
    display: flex;
    flex-direction: column;
    justify-content: center;

    h1 {
        text-shadow: 0 0 18px var(--color-highlight), 0 0 18px var(--rival);
        letter-spacing: .1em;
        font-weight: bold;
        font-size: 30px;
    }

    div.muted {
        font-size: 14px;
    }
}
</style>
