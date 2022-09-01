<template>
  <div>
    <div class="msg">{{ flag }}</div>
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
               @click="moveOrAttackWhite($event)"></div>
          <div class="checker-black"
               v-if="ifBlack (col, row, i_col)"
               @click="moveOrAttackBlack($event)"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'BoardCheckers',
  data: () => ({
    rows: [1, 2, 3, 4, 5, 6, 7, 8].reverse(),
    columns: ['a', 'b', 'c', 'd', 'e', 'f', 'g', 'h'],
    flag: 'ход белых'
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
      const Checkers = [...document.querySelectorAll('.checker-white'), ...document.querySelectorAll('.checker-black')]
      Checkers.forEach((elem) => {
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
    attack (event) {
      const Checkers = [...document.querySelectorAll('.checker-white'), ...document.querySelectorAll('.checker-black')]
      Checkers.forEach((elem) => {
        elem.classList.remove('take')
      })
      const e = event.target
      e.classList.add('take')
      const allSq = document.querySelectorAll('.board__square')
      allSq.forEach((elem) => {
        elem.classList.remove('dropPos')
      })
      const id0 = e.parentNode.id.codePointAt(0)
      const id1 = e.parentNode.id.codePointAt(1)
      const upLeftx2 = String.fromCodePoint(id0 - 2) + String.fromCodePoint(id1 + 2)
      const upRightx2 = String.fromCodePoint(id0 + 2) + String.fromCodePoint(id1 + 2)
      const downLeftx2 = String.fromCodePoint(id0 - 2) + String.fromCodePoint(id1 - 2)
      const downRightx2 = String.fromCodePoint(id0 + 2) + String.fromCodePoint(id1 - 2)
      const getUpRightx2 = document.getElementById(upRightx2)
      const getUpLeftx2 = document.getElementById(upLeftx2)
      const getDownRightx2 = document.getElementById(downRightx2)
      const getDownLeftx2 = document.getElementById(downLeftx2)
      const idUpLeft = String.fromCodePoint(id0 - 1) + String.fromCodePoint(id1 + 1)
      const idUpRight = String.fromCodePoint(id0 + 1) + String.fromCodePoint(id1 + 1)
      const idDownLeft = String.fromCodePoint(id0 - 1) + String.fromCodePoint(id1 - 1)
      const idDownRight = String.fromCodePoint(id0 + 1) + String.fromCodePoint(id1 - 1)
      const getUpRight = document.getElementById(idUpRight)
      const getUpLeft = document.getElementById(idUpLeft)
      const getDownRight = document.getElementById(idDownRight)
      const getDownLeft = document.getElementById(idDownLeft)
      if (getUpRightx2 && getUpRight.children.length > 0 && getUpRightx2.children.length === 0) {
        getUpRightx2.classList.add('dropPos')
        getUpRight.classList.add('kill-target')
      }
      if (getUpLeftx2 && getUpLeft.children.length > 0 && getUpLeftx2.children.length === 0) {
        getUpLeftx2.classList.add('dropPos')
        getUpLeft.classList.add('kill-target')
      }
      if (getDownRightx2 && getDownRight.children.length > 0 && getDownRightx2.children.length === 0) {
        getDownRightx2.classList.add('dropPos')
        getDownRight.classList.add('kill-target')
      }
      if (getDownLeftx2 && getDownLeft.children.length > 0 && getDownLeftx2.children.length === 0) {
        getDownLeft.classList.add('kill-target')
        getDownLeftx2.classList.add('dropPos')
      }
    },
    drop (event) {
      let check = false
      let opponentsСheckers
      let alienChecker

      if (this.flag === 'ход белых' || this.flag === 'удар белых') {
        opponentsСheckers = 'checker-white'
        alienChecker = '.checker-black'
      } else if (this.flag === 'ход черных' || this.flag === 'удар черных') {
        opponentsСheckers = 'checker-black'
        alienChecker = '.checker-white'
      }
      const x = document.querySelector('.take')
      if (event.target.classList.contains('dropPos')) {
        event.target.insertAdjacentElement('beforeend', x)
        document.querySelectorAll('.kill-target').forEach((e) => {
          e.classList.remove('kill-target')
          e.innerHTML = ''
        })
        document.querySelectorAll('.take').forEach((e) => {
          e.classList.remove('take')
        })
        document.querySelectorAll('.dropPos').forEach((e) => {
          e.classList.remove('dropPos')
        })
        let checkers = this.$el.querySelectorAll(alienChecker)
        checkers = Array.from(checkers)
        let id0
        let id1
        let idUpLeft
        let idUpRight
        let idDownLeft
        let idDownRight
        let getUpRight
        let getUpLeft
        let getDownRight
        let getDownLeft
        let upLeftx2
        let upRightx2
        let downLeftx2
        let downRightx2
        let getUpRightx2
        let getUpLeftx2
        let getDownRightx2
        let getDownLeftx2

        function checkItems (elem, idex, arr) {
          id0 = elem.parentNode.id.codePointAt(0)
          id1 = elem.parentNode.id.codePointAt(1)
          idUpLeft = String.fromCodePoint(id0 - 1) + String.fromCodePoint(id1 + 1)
          idUpRight = String.fromCodePoint(id0 + 1) + String.fromCodePoint(id1 + 1)
          idDownLeft = String.fromCodePoint(id0 - 1) + String.fromCodePoint(id1 - 1)
          idDownRight = String.fromCodePoint(id0 + 1) + String.fromCodePoint(id1 - 1)
          getUpRight = document.getElementById(idUpRight)
          getUpLeft = document.getElementById(idUpLeft)
          getDownRight = document.getElementById(idDownRight)
          getDownLeft = document.getElementById(idDownLeft)
          upLeftx2 = String.fromCodePoint(id0 - 2) + String.fromCodePoint(id1 + 2)
          upRightx2 = String.fromCodePoint(id0 + 2) + String.fromCodePoint(id1 + 2)
          downLeftx2 = String.fromCodePoint(id0 - 2) + String.fromCodePoint(id1 - 2)
          downRightx2 = String.fromCodePoint(id0 + 2) + String.fromCodePoint(id1 - 2)
          getUpRightx2 = document.getElementById(upRightx2)
          getUpLeftx2 = document.getElementById(upLeftx2)
          getDownRightx2 = document.getElementById(downRightx2)
          getDownLeftx2 = document.getElementById(downLeftx2)
          if (getUpRight && getUpRight.children.length &&
            getUpRight.firstChild.classList.contains(opponentsСheckers) &&
            getUpRightx2 && !getUpRightx2.children.length) {
            return true
          }
          if (getUpLeft && getUpLeft.children.length &&
            getUpLeft.firstChild.classList.contains(opponentsСheckers) &&
            getUpLeftx2 && !getUpLeftx2.children.length) {
            return true
          }
          if (getDownRight && getDownRight.children.length &&
            getDownRight.firstChild.classList.contains(opponentsСheckers) &&
            getDownRightx2 && !getDownRightx2.children.length) {
            return true
          }
          if (getDownLeft && getDownLeft.children.length &&
            getDownLeft.firstChild.classList.contains(opponentsСheckers) &&
            getDownLeftx2 && !getDownLeftx2.children.length) {
            return true
          }
        }

        check = checkers.some(checkItems)
        if (this.flag === 'ход черных' || this.flag === 'удар черных') {
          if (check === true) {
            this.flag = 'удар белых'
          } else {
            this.flag = 'ход белых'
          }
        } else if (this.flag === 'ход белых' || this.flag === 'удар белых') {
          if (check === true) {
            this.flag = 'удар черных'
          } else {
            this.flag = 'ход черных'
          }
        }
      }
    },
    moveOrAttackBlack (event) {
      if (this.flag === 'ход черных') {
        return this.takeChecker(event)
      }
      if (this.flag === 'удар черных') {
        return this.attack(event)
      }
    },
    moveOrAttackWhite (event) {
      if (this.flag === 'ход белых') {
        return this.takeChecker(event)
      }
      if (this.flag === 'удар белых') {
        return this.attack(event)
      }
    }
  },
  mounted () {
    // this.$el.addEventListener('DOMContentLoaded', this.delComment(), false)
    const treeWalker = document.createTreeWalker(document, NodeFilter.SHOW_COMMENT, null, false)
    const nodeList = []
    while (treeWalker.nextNode()) nodeList.push(treeWalker.currentNode)
    nodeList.forEach(function (a) {
      a.parentNode.removeChild(a)
    })
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

  .kill-target {
    background-color: antiquewhite;
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

</style>
