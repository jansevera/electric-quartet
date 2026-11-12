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

const carsArray = ref(cars.map(c => c.car_id))

function shuffleArray(array) {
    const arr = [...array];
    for (let i = arr.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [arr[i], arr[j]] = [arr[j], arr[i]];
    }
    return arr;
}

const carsShuffled = shuffleArray(carsArray.value)

const userDeck = ref([])
const oponentDeck = ref([])
const potDeck = ref([])

while (carsShuffled.length > 0) {
    userDeck.value.push(carsShuffled.pop())
    if (carsShuffled.length > 0) oponentDeck.value.push(carsShuffled.pop())
}

const userCards = computed(() => userDeck.value.length)
const maxCards = computed(() => carsArray.value.length)

const rivalCards = computed(() => oponentDeck.value.length)

const revealed = ref(false)
const pickedStat = ref(null)

const user = "user"
const oponent = "oponent"

const compareResult = ref(null)
const units = ref({ acc: 's', speed: 'km/h', range: 'km', battery: 'kWh' })
const features = ref({
    range: "Range (WLTP)",
    acc: "0-100 km/h",
    speed: "Top speed",
    battery: "Battery"
})

function onPick(statId) {
    pickedStat.value = statId
    revealed.value = true
    compareValues(statId)
    compareResult.value = compareValues(statId)
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
function play() {
    selectedId.value = userDeck.value.shift()
    oponentId.value = oponentDeck.value.shift()
}

function moveCardsToDeck(deck) {
    deck.push(selectedId.value)
    deck.push(oponentId.value)
}

function nextRound(deck) {
    moveCardsToDeck(deck)
    revealed.value = false
    pickedStat.value = null
    compareResult.value = null
    play()
}

play()
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
            <div>User deck:<br>{{ userDeck }}</div>
        </div>
        <div class="player-zone">
            <div class="zone-caption text-center uppercase muted">You play</div>

            <div class="play-card">
                <CardFront v-if="car" :car="car" :picked="pickedStat" :compare="compareResult" :player="user" hide />
                <!-- hide footer? -->
            </div>
        </div>
        <div class="duel-center">
            <div class="result" v-if="compareResult === 'win'" :class="revealed ? compareResult : ''" role="status">
                <h1 class="big highlight">You win the round</h1>
                <div class="cmp"><span class="highlight">{{ car[pickedStat] }}</span> &nbsp;<span
                        class="muted">vs</span>&nbsp;
                    <span class="rival">{{
                        oponentCar[pickedStat] }}</span> <span class="muted">{{ units[pickedStat] }}</span>
                </div>
                <div class="muted why" v-if="pickedStat === 'acc'">0–100 km/h · Lower time wins<br></div>
                <div class="muted" v-else>{{ features[pickedStat] }} · Higher value wins</div>
                <div class="muted">+2 cards to your deck</div>

                <div><button @click="nextRound(userDeck)">Next round</button></div>
            </div>
            <div class=" result" v-else-if="compareResult === 'lose'" :class="revealed ? compareResult : ''">
                <h1 class="big rival">Rival wins the round</h1>
                <div class="cmp"><span class="highlight">{{ car[pickedStat] }}</span> &nbsp;<span
                        class="muted">vs</span>&nbsp;
                    <span class="rival">{{
                        oponentCar[pickedStat] }}</span> <span class="muted">{{ units[pickedStat] }}</span>
                </div>
                <div class="muted why" v-if="pickedStat === 'acc'">0–100 km/h · Lower time wins<br></div>
                <div class="muted" v-else>{{ features[pickedStat] }} · Higher value wins</div>
                <div class="muted">2 cards go to the rival</div>
                <div><button @click="nextRound(oponentDeck)">Next round</button></div>
            </div>
            <div v-else-if="compareResult === 'tie'" :class="revealed ? compareResult : ''">
                <h1 class="big rival">Tie</h1>
                <div class="cmp"><span class="highlight">{{ car[pickedStat] }}</span> &nbsp;<span
                        class="muted">vs</span>&nbsp;
                    <span class="rival">{{
                        oponentCar[pickedStat] }}</span> <span class="muted">{{ units[pickedStat] }}</span>
                </div>
                <div class="muted why" v-if="pickedStat === 'acc'">0–100 km/h · Lower time wins<br></div>
                <div class="muted" v-else>{{ features[pickedStat] }} · Higher value wins</div>
                <div class="muted">Both cards go to the pot. <br>You pick again.</div>
                <div><button @click="nextRound(potDeck)">Next round</button></div>
            </div>
            <div v-else>
                <h1>VS</h1>
                <div class="muted">Rival card stays face down until you pick</div>
            </div>
        </div>
        <div class="player-zone rival"><!-- Rival -->
            <div class="zone-caption text-center uppercase muted">Rival plays</div>
            <div class="play-card">
                <CardFront v-if="revealed && oponentCar" :car="oponentCar" :picked="pickedStat" :compare="compareResult"
                    :player="oponent" hide />
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
            <p v-if="revealed">Stat chosen</p>
            <p v-else><strong class="highlight">Your turn.</strong> Pick the stat you think beats the hidden
                card.</p>
            <GameTableFooter v-if="car" :range="car.range" :acc="car.acc" :speed="car.speed" :battery="car.battery"
                :picked="pickedStat" @pick="onPick" />
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

    .result {
        display: flex;
        flex-direction: column;
        gap: 6px;
        align-items: center;
        padding: 14px 14px;
        border-radius: 14px;
        border: 1px solid var(--c);
        background:
            color-mix(in srgb, var(--c) 10%, transparent);
        width: 100%;

        &.win {
            --c: var(--color-highlight)
        }

        &.lose {
            --c: var(--rival)
        }

        &.tie {
            --c: var(--tie)
        }

        h1 {
            font-size: 20px;
            letter-spacing: normal;
            line-height: normal;
            text-shadow: none;
        }
    }
}
</style>
