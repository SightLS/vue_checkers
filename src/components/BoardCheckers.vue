<template>
  <div class="board">
    <div class="board__rows"
         v-for="row in rows" :key="row">
      <div class="board__square"
           v-for="(col, i_col) in columns"
           :key="col"
           :id="col + row"
           :class="[i_col % 2 !== row % 2 ? 'black' : 'white']"
      >
        <div class="checker-white"
             :draggable="draggeble"
             v-if="row === 2 && i_col % 2 ||
             (row === 1 || row === 3) &&
             !(i_col % 2)"></div>
        <div class="checker-black"
             v-if="(row === rows.length || row === rows.length - 2) && i_col % 2 ||
             row === rows.length - 1 &&
             !(i_col % 2)"></div>
      </div>
    </div>
    <div class="checker black"></div>
  </div>
</template>

<script>

export default {
  name: 'BoardCheckers',
  data: () => ({
    rows: [1, 2, 3, 4, 5, 6, 7, 8].reverse(),
    columns: ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h'],
    draggeble: true
  }),
  methods: () => ({})
}
</script>

<style scoped lang="scss">
.board {
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;

  &__rows {
    display: flex;
    flex-direction: row;
  }

  &__square {
    justify-content: center;
    align-items: center;
    display: flex;
    flex-direction: row;
    width: 50px;
    height: 50px;
    border: 1px solid black;
  }

  .black {
    background-color: red;
  }
}

.checker-black {
  border: 1px solid black;
  background-color: black;
  width: 45px;
  height: 45px;
  border-radius: 30px;
  z-index: 100;
}

.checker-white {
  @extend .checker-black;
  background-color: blue;
}
</style>
