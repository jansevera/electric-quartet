# Electric Quartet

A browser card game in the style of a classic "quartet" (top trumps) deck, built around
electric cars. You play against a computer rival: each round you compare one stat of
your top card against the rival's hidden card, and the winner takes both cards.

Built with **Vue 3** + **Vite**, styled with **SCSS**.

## How to play

1. The 32-card deck is shuffled and split evenly between you and the rival.
2. Each round, both players put their top card on the table. You see your card; the
   rival's card stays face down.
3. The player whose turn it is picks one stat to compare:

   | Stat | Unit | Winner |
   |------|------|--------|
   | Range (WLTP) | km | higher value |
   | 0–100 km/h | s | **lower** time |
   | Top speed | km/h | higher value |
   | Battery | kWh | higher value |

4. The rival's card is revealed and the result is shown:
   - **Win** – both cards go to your deck and you pick again next round.
   - **Lose** – both cards go to the rival, and the rival picks next (it "thinks" for
     1–2.5 s and then chooses a random stat).
   - **Tie** – both cards go to the **tie pot** and the same player picks again with
     new cards. Whoever wins the next decided round also takes everything in the pot.
5. The game ends when one player has no cards left. Click **Play again** to deal a new game.

The **Auto next round** checkbox moves on to the next round automatically after a
10-second countdown. Untick it to continue only with the **Next round** button.

## The deck

32 current electric cars in 8 groups (A–H) of 4 cards. Each card is defined in
[src/data/cars.json](src/data/cars.json):

```json
{
  "car_id": "A1",
  "car_name": "Tesla Model 3",
  "car_version": "Premium AWD (Highland)",
  "range": 660,
  "acc": 4.4,
  "speed": 201,
  "battery": 79
}
```

- Card images live in [public/images/](public/images/) and are named after the
  `car_id` (e.g. `A1.webp`). If an image is missing, `default-image.svg` is shown instead.
- [src/data/maxValues.json](src/data/maxValues.json) holds the maximum value of each
  stat, which sets the scale of the stat bars on the cards.

### Adding or changing a card

1. Add or edit an entry in `cars.json` with a unique `car_id`.
2. Add a matching `public/images/<car_id>.webp` image.
3. If a new value is higher than the one in `maxValues.json`, raise the maximum there.

## Project structure

```
src/
├── main.js                  # app entry point
├── Main.vue                 # game table and all game logic (deal, rounds, rival, countdown)
├── CardFront.vue            # face-up car card
├── CardBack.vue             # face-down card
├── CardNo.vue               # empty pile placeholder
├── components/
│   ├── GameTableHeader.vue  # card counts and round number
│   ├── GameTableFooter.vue  # stat picker buttons
│   ├── Header.vue           # card title (name, version, id)
│   ├── MainSection.vue      # stat rows on a card
│   ├── Footer.vue
│   ├── FeatureItem.vue
│   └── icons/               # stat icons
├── data/
│   ├── cars.json            # the deck
│   └── maxValues.json       # stat maximums for the bars
└── assets/                  # global SCSS (base.scss, main.scss)
public/
└── images/                  # car images (<car_id>.webp)
```

## Development

Requires Node.js `^22.18.0` or `>=24.12.0`.

```sh
npm install      # install dependencies
npm run dev      # start the dev server with hot reload
npm run build    # build for production into dist/
npm run preview  # preview the production build locally
```

The Vite `base` is set to `/electric-quartet/` (see [vite.config.js](vite.config.js)),
so the app expects to be served from that path, e.g. on GitHub Pages at
`https://<user>.github.io/electric-quartet/`. Change `base` if you host it elsewhere.

### Debugging round results

Add `?force=win`, `?force=lose` or `?force=tie` to the URL to force the result of every
round. This is useful for testing the end-of-game screens and the tie pot.

### Recommended IDE setup

[VS Code](https://code.visualstudio.com/) with the
[Vue (Official)](https://marketplace.visualstudio.com/items?itemName=Vue.volar) extension.
[Vue.js devtools](https://devtools.vuejs.org/) are also enabled in development through
`vite-plugin-vue-devtools`.
