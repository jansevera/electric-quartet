<template>
  <div class="item">

    <div class="details">
      <div class="label">
        <i>
          <slot name="icon"></slot>
        </i>
        <h3>
          <slot name="label"></slot>
        </h3>
      </div>
      <div class="value">
        <slot name="value"></slot>
      </div>
    </div>
    <div class="bar-track">
      <slot name="bar"></slot>
    </div>
  </div>
</template>

<style scoped>
.item {
  margin-top: 10px;
  display: flex;
  position: relative;
  flex-wrap: wrap;
}

.details {
  display: flex;
  flex: 1;
  flex-direction: row;
  justify-content: space-between;

}

.label {
  display: flex;
  flex-direction: row;
  align-items: center;
  line-height: 1.9em;
}

i {
  display: flex;
  place-items: center;
  place-content: center;
  width: 32px;
  color: var(--highlight-color);
}

h3 {
  font-size: 13px;
  font-weight: 500;
  color: var(--color-heading);
}

.value {
  font-weight: bold;
}

.bar-track {
  background-color: #222A33;
  height: 6px;
  width: 100%;
  border-radius: 3px;
}

:slotted(.bar) {
  background-color: #3DF5C8;
  border-radius: 3px;
  height: 6px;
}

@media (min-width: 1024px) {}
</style>
