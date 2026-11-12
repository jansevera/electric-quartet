<script setup>
import CardFront from './CardFront.vue';
import cars from '@/data/cars.json'
import CardBack from './CardBack.vue';
import { ref, computed, onBeforeUnmount } from 'vue'
import GameTableFooter from './components/GameTableFooter.vue';
import GameTableHeader from './components/GameTableHeader.vue';
import CardNo from './CardNo.vue';

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

const userDeck = ref([])
const oponentDeck = ref([])
const tieDeck = ref([])
const numberOfRounds = ref(0)

function dealCards() {
    const carsShuffled = shuffleArray(carsArray.value)
    userDeck.value = []
    oponentDeck.value = []
    while (carsShuffled.length > 0) {
        userDeck.value.push(carsShuffled.pop())
        if (carsShuffled.length > 0) oponentDeck.value.push(carsShuffled.pop())
    }
}

const maxCards = computed(() => carsArray.value.length)
// one card per player is on the table while the game is running
const cardOnTable = computed(() => (gameFinished.value ? 0 : 1))

const userCards = computed(() => userDeck.value.length + cardOnTable.value)
const rivalCards = computed(() => oponentDeck.value.length + cardOnTable.value)
const tieCards = computed(() => tieDeck.value.length)

const revealed = ref(false)
const pickedStat = ref(null)
const gameFinished = ref(false)

const user = "user"
const oponent = "oponent"

const compareResult = ref(null)
const units = ref({ acc: 's', speed: 'km/h', range: 'km', battery: 'kWh' })
const features = {
    range: "Range (WLTP)",
    acc: "0-100 km/h",
    speed: "Top speed",
    battery: "Battery"
}

function onPick(statId) {
    pickedStat.value = statId
    revealed.value = true
    compareResult.value = compareValues(statId)
}

// debug: ?force=tie|win|lose in the URL forces every round's result
const forceParam = new URLSearchParams(location.search).get('force')
const forceResult = ['tie', 'win', 'lose'].includes(forceParam) ? forceParam : null

function compareValues(statId) {
    if (forceResult) return forceResult
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
    numberOfRounds.value = numberOfRounds.value + 1
    if (userDeck.value.length === 0 || oponentDeck.value.length === 0) {
        //game finished
        gameFinished.value = true
        return false
    }
    selectedId.value = userDeck.value.shift()
    oponentId.value = oponentDeck.value.shift()
    return true
}

// who picks the stat this round: 'user' or 'oponent'
const currentPicker = ref(user)

function moveCardsToDeck(deck) {
    deck.push(selectedId.value)
    deck.push(oponentId.value)

    // the round winner also takes all cards waiting in the tie pot
    if (deck !== tieDeck.value) {
        deck.push(...tieDeck.value.splice(0))
    }
}

function nextRound(deck) {
    moveCardsToDeck(deck)

    if (compareResult.value === 'win') {
        currentPicker.value = user
        revealed.value = false
        pickedStat.value = null
        compareResult.value = null
        play()
    } else if (compareResult.value === 'tie') {
        // same player picks again with fresh cards
        pickedStat.value = null
        compareResult.value = null
        if (currentPicker.value === oponent) {
            oponentPlay()
        } else {
            revealed.value = false
            play()
        }
    }
    else {
        //revealed.value = false
        pickedStat.value = null
        compareResult.value = null
        oponentPlay()
    }
}

function randomFeature() {
    //random feature for rival
    const keys = Object.keys(features)          // ['range', 'acc', 'speed', 'battery']
    return keys[Math.floor(Math.random() * keys.length)]

}

const oponentThinking = ref(false)
let oponentTimer = null

function oponentPlay() {
    currentPicker.value = oponent
    if (!play()) return //return when game finished

    revealed.value = false
    oponentThinking.value = true

    // random "thinking" time between 1 and 2.5 s
    const delay = 1000 + Math.random() * 1500
    oponentTimer = setTimeout(() => {
        oponentThinking.value = false
        oponentTimer = null
        onPick(randomFeature())
    }, delay)
}

onBeforeUnmount(() => clearTimeout(oponentTimer))

// reset everything to the initial state and deal a fresh shuffled deck
function newGame() {
    clearTimeout(oponentTimer)
    oponentTimer = null
    oponentThinking.value = false

    tieDeck.value = []
    numberOfRounds.value = 0
    revealed.value = false
    pickedStat.value = null
    compareResult.value = null
    currentPicker.value = user
    gameFinished.value = false

    dealCards()
    play()
}

newGame()
</script>

<template>

    <div class="game-table">
        <GameTableHeader :userCards="userCards" :rivalCards="rivalCards" :tieCards="tieCards" :maxCards="maxCards"
            :numberOfRounds="numberOfRounds" />
        <!-- You -->
        <div class="pile">
            <div>Your deck</div>
            <div class="deck-pile">
                <template v-if="userDeck.length">
                    <CardBack v-for="n in Math.min(userDeck.length, 3)" :key="n" />
                </template>
                <CardNo v-else />
            </div>
            <div><strong>{{ userCards - cardOnTable }}</strong> cards</div>
        </div>
        <div class="player-zone">
            <div class="zone-caption text-center uppercase muted" v-if="!gameFinished">You play</div>

            <div class="play-card" v-if="!gameFinished">
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

                <div><button @click="nextRound(userDeck)" class="next">Next round</button></div>
            </div>
            <div class="result" v-else-if="compareResult === 'lose'" :class="revealed ? compareResult : ''">
                <h1 class="big rival">Rival wins the round</h1>
                <div class="cmp"><span class="highlight">{{ car[pickedStat] }}</span> &nbsp;<span
                        class="muted">vs</span>&nbsp;
                    <span class="rival">{{
                        oponentCar[pickedStat] }}</span> <span class="muted">{{ units[pickedStat] }}</span>
                </div>
                <div class="muted why" v-if="pickedStat === 'acc'">0–100 km/h · Lower time wins<br></div>
                <div class="muted" v-else>{{ features[pickedStat] }} · Higher value wins</div>
                <div class="muted">2 cards go to the rival</div>
                <div><button @click="nextRound(oponentDeck)" class="next">Next round</button></div>
            </div>
            <div class="result" v-else-if="compareResult === 'tie'" :class="revealed ? compareResult : ''">
                <h1 class="big tie">Tie</h1>
                <div class="cmp"><span class="highlight">{{ car[pickedStat] }}</span> &nbsp;<span
                        class="muted">vs</span>&nbsp;
                    <span class="rival">{{
                        oponentCar[pickedStat] }}</span> <span class="muted">{{ units[pickedStat] }}</span>
                </div>
                <div class="muted why" v-if="pickedStat === 'acc'">0–100 km/h · Lower time wins<br></div>
                <div class="muted" v-else>{{ features[pickedStat] }} · Higher value wins</div>
                <div class="muted">Both cards go to the pot. <br>
                    {{ currentPicker === user ? 'You pick again.' : 'Rival picks again.' }}
                </div>
                <div><button @click="nextRound(tieDeck)" class="next">Next round</button></div>
            </div>

            <div v-else-if="gameFinished">
                Game finished.
                <div v-if="userCards === 0 && rivalCards === 0" class="result tie">
                    <h1 class="big">Draw!</h1>
                    <button @click="newGame" class="next">Play again</button>
                </div>
                <div v-else-if="userCards > 0" class="result win">
                    <h1 class="big highlight">You won the whole game.</h1>
                    <button @click="newGame" class="next">Play again</button>
                </div>
                <div v-else class="result lose">
                    <h1 class="big rival">You lost the game.</h1> Better luck next time! <br>
                    <button @click="newGame" class="next">Play again</button>
                </div>
            </div>

            <div v-else-if="oponentThinking">
                <h1 class="rival">VS</h1>
                <div class="rival"><strong>Rival</strong> is choosing a stat…</div>
            </div>

            <div v-else>
                <h1>VS</h1>
                <div class="muted">Rival card stays face down until you pick</div>
            </div>
        </div>
        <div class="player-zone rival"><!-- Rival -->
            <div class="zone-caption text-center uppercase muted" v-if="!gameFinished">Rival plays</div>
            <div class="play-card" v-if="!gameFinished">
                <CardFront v-if="revealed && oponentCar" :car="oponentCar" :picked="pickedStat" :compare="compareResult"
                    :player="oponent" hide />
                <!-- hide footer? -->
                <CardBack v-else />
            </div>
        </div>
        <div class="pile">Rival deck
            <div class="deck-pile">
                <template v-if="oponentDeck.length">
                    <CardBack v-for="n in Math.min(oponentDeck.length, 3)" :key="n" />
                </template>
                <CardNo v-else />
            </div>
            <div><strong>{{ rivalCards - cardOnTable }}</strong> cards</div>
        </div>

        <div class="stat-picker">
            <p v-if="revealed && !gameFinished">Stat chosen</p>
            <p v-else-if="oponentThinking"><strong class="rival">Rival's turn.</strong> Waiting for the rival to
                pick a stat…</p>
            <p v-else><span v-if="!gameFinished"><strong class="highlight">Your turn.</strong> Pick the stat you think
                    beats the
                    hidden
                    card.</span></p>
            <GameTableFooter v-if="car" :range="car.range" :acc="car.acc" :speed="car.speed" :battery="car.battery"
                :picked="pickedStat" :disabled="oponentThinking || gameFinished" @pick="onPick" />
        </div>

    </div>

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
            color: var(--c);
        }
    }

    button.next {
        background-color: var(--color-text);
        color: var(--table-bg);
        font-family: inherit;
        font-weight: bold;
        border: 0;
        border-radius: 999px;
        padding: 9px 18px;
        cursor: pointer;
        margin-top: 1em;

        &:hover {
            background-color: var(--c);
        }
    }
}
</style>
