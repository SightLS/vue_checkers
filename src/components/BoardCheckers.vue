<template>
  <div class="board">
    <div class="board__rows"
         v-for="row in rows" :key="row">
      <div class="board__square"
           v-for="(col, i_col) in columns"
           :key="col"
           :id="col + row"
           :class="[getClass(col, row, i_col)]"
           @click="drop($event)"
      >
        <div class="checker-white"
             :draggable="draggeble"
             v-if="ifWhite (col, row, i_col)"
             @click="clickWhite ($event)"></div>
        <div class="checker-black"
             v-if="ifBlack (col, row, i_col)"
             @click="clickWhite ($event)"></div>
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
    draggeble: false
  }),
  methods: {
    getClass (col, row, indexCol) {
      return (indexCol % 2 !== row % 2) ? 'black' : 'white'
    },
    ifWhite (col, row, indexCol) {
      const row2 = row === 2 && indexCol % 2
      const row3 = row === 3 && !(indexCol % 2)
      const row1 = row === 1 && !(indexCol % 2)
      if (row3 || row1 || row2) {
        return true
      }
    },
    ifBlack (col, row, indexCol) {
      const row2 = row === this.rows.length && indexCol % 2
      const row3 = row === this.rows.length - 2 && indexCol % 2
      const row1 = row === this.rows.length - 1 && !(indexCol % 2)
      if (row2 || row3 || row1) {
        return true
      }
    },
    clickWhite (event) {
      const e = event.target
      e.classList.toggle('hide')
      if (this.draggeble) {
        this.draggeble = false
      } else {
        this.draggeble = true
      }
    },
    dropShadow () {
      if (this.draggeble === true) {
        return 'drop'
      } else {
        return 'undrop'
      }
    },
    drop (event) {
      const x = document.querySelector('.hide')
      console.log(x, '2')
      event.target.insertAdjacentElement('beforeend', x)
    }
  }
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

.hide {
  background-color: gray;
}

.drop {
  background: rgba(161, 21, 207, 0.5);
}

</style>
