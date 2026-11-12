<script setup>
</script>

<template>
  <div class="card no-left" aria-label="No back">
    <div class="frame">
      <div class="center">
        <span class="word">No <br>card <br>left</span>
      </div>

    </div>
  </div>
</template>

<style scoped>
.no-left {
  overflow: hidden;
  background: radial-gradient(circle at 50% 50%, color-mix(in srgb, var(--color-muted) 22%, transparent) 0%, transparent 42%), repeating-linear-gradient(60deg, color-mix(in srgb, var(--color-muted) 9%, transparent) 0 1px, transparent 1px 1.6em), repeating-linear-gradient(-60deg, color-mix(in srgb, var(--color-muted) 9%, transparent) 0 1px, transparent 1px 1.6em), var(--color-card-background);
  max-height: 560px;
  width: 100%;
  aspect-ratio: 360 / 520;
  font-size: calc(14px * var(--s));

  .frame {
    position: absolute;
    inset: 1em;
    border: 1px solid color-mix(in srgb, var(--color-card-background) 55%, transparent);
    border-radius: .85em;
    box-shadow: inset 0 0 1.6em -.4em color-mix(in srgb, var(--color-muted) 45%, transparent);

  }

  .word {
    font-size: 2.35em;
    font-weight: 700;
    letter-spacing: .55em;
    padding-left: .55em;
    text-transform: uppercase;
  }

  .center {
    position: absolute;
    inset: 0;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 1.3em
  }
}

@media (min-width: 1024px) {}
</style>
