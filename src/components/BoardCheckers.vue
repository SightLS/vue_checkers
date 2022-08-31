<template>
  <div class="board">
    <div class="board__rows"
         v-for="row in rows" :key="row">
      <div class="board__square"
           v-for="(col, i_col) in columns"
           :key="col"
           :id="col + row"
           :class="[getClass(col, row, i_col)]"
           @click="drop"
      >
        <div class="checker-white"
             v-if="ifWhite (col, row, i_col)"
             @click="takeChecker ($event)"></div>
        <div class="checker-black"
             v-if="ifBlack (col, row, i_col)"
             @click="takeChecker ($event)"></div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'BoardCheckers',
  data: () => ({
    rows: [1, 2, 3, 4, 5, 6, 7, 8].reverse(),
    columns: ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h']
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
    takeChecker (event) {
      const anotherCheckers = document.querySelectorAll('.checker-white')
      anotherCheckers.forEach((elem) => {
        elem.classList.remove('take')
      })
      const e = event.target
      e.classList.add('take')
      let sum
      if (e.classList.contains('checker-white')) {
        sum = 1
      } else {
        sum = -1
      }
      const allSq = document.querySelectorAll('.board__square')
      allSq.forEach((elem) => {
        elem.classList.remove('dropPos')
      })
      const id0 = e.parentNode.id.codePointAt(0)
      const id1 = e.parentNode.id.codePointAt(1)

      const idRight = String.fromCodePoint(id0 - sum) + String.fromCodePoint(id1 + sum)
      const idLeft = String.fromCodePoint(id0 + sum) + String.fromCodePoint(id1 + sum)
      const getUpRight = document.getElementById(idRight)
      const getUpLeft = document.getElementById(idLeft)
      if (getUpRight && getUpLeft) {
        if (!getUpRight.children.length) {
          getUpRight.classList.add('dropPos')
        }
        if (!getUpLeft.children.length) {
          getUpLeft.classList.add('dropPos')
        }
      } else if (!getUpRight && getUpLeft) {
        if (!getUpLeft.children.length) {
          getUpLeft.classList.add('dropPos')
        }
      } else if (getUpRight && !getUpLeft) {
        if (!getUpRight.children.length) {
          getUpRight.classList.add('dropPos')
        }
      }
    },
    drop (event) {
      const x = document.querySelector('.take')
      if (event.target.classList.contains('dropPos')) {
        event.target.insertAdjacentElement('beforeend', x)
        document.querySelectorAll('.take').forEach((e) => {
          e.classList.remove('take')
        })
        document.querySelectorAll('.dropPos').forEach((e) => {
          e.classList.remove('dropPos')
        })
      }
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

  .dropPos {
    background-color: purple;
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

.take {
  background-color: gray;
}

.drop {
  background: rgba(161, 21, 207, 0.5);
}

</style>
