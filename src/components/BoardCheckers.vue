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
             @click="checkDrop ($event)"
        >
          <div class="checker-white"
               v-if="ifWhite (col, row, i_col)"
               @click="moveOrAttackWhite($event)"></div>
          <div class="checker-black"
               v-if="ifBlack (col, row, i_col)"
               @click="moveOrAttackBlack($event)"></div>
        </div>
      </div>
      <div>checker-white damka-white</div>
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
        elem.parentNode.classList.remove('take')
      })
      const e = event.target
      e.parentNode.classList.add('take')
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
      if (e.classList.contains('damka-white') || e.classList.contains('damka-black')) {
        let id
        let getId
        let a = id0
        let b = id1
        for (let i = 0; i < 4; i++) {
          if (i === 0) {
            damkaMove(a, b)
          } else if (i === 1) {
            a = -a
            damkaMove(a, b)
          } else if (i === 2) {
            b = -b
            damkaMove(a, b)
          } else {
            a = -a
            damkaMove(a, b)
          }
        }

        function damkaMove (id0, id1) {
          let counter = 1
          while (counter <= 8) {
            id = String.fromCodePoint(Math.abs(id0 + counter)) + String.fromCodePoint(Math.abs(id1 + counter))
            getId = document.getElementById(id)
            if (getId && !getId.children.length) {
              getId.classList.add('dropPos')
              counter++
            } else {
              break
            }
          }
        }
      }
    },
    attack (event) {
      let checkers
      let opponent
      if (this.flag === 'удар черных') {
        checkers = this.$el.querySelectorAll('.checker-black')
        opponent = 'checker-white'
      } else if (this.flag === 'удар белых') {
        checkers = this.$el.querySelectorAll('.checker-white')
        opponent = 'checker-black'
      } else if (this.flag === 'черные продолжайте бить') {
        checkers = this.$el.querySelectorAll('.last-move-black')
        opponent = 'checker-white'
      } else if (this.flag === 'белые продолжайте бить') {
        checkers = this.$el.querySelectorAll('.last-move-white')
        opponent = 'checker-black'
      }

      checkers.forEach((elem) => {
        elem.parentNode.classList.remove('take')
      })
      const e = event.target
      e.parentNode.classList.add('take')
      const allSq = document.querySelectorAll('.board__square')
      allSq.forEach((elem) => {
        elem.classList.remove('dropPos')
        elem.classList.remove('kill-target')
      })
      const id0 = e.parentNode.id.codePointAt(0)
      const id1 = e.parentNode.id.codePointAt(1)

      let id
      let idx2
      let getId
      let getIdx2
      let a = id0
      let b = id1
      for (let i = 0; i < 4; i++) {
        if (i === 0) {
          checkerAttacks(a, b)
        } else if (i === 1) {
          a = -a
          checkerAttacks(a, b)
        } else if (i === 2) {
          b = -b
          checkerAttacks(a, b)
        } else {
          a = -a
          checkerAttacks(a, b)
        }

        function checkerAttacks (id0, id1) {
          id = String.fromCodePoint(Math.abs(id0 + 1)) + String.fromCodePoint(Math.abs(id1 + 1))
          idx2 = String.fromCodePoint(Math.abs(id0 + 2)) + String.fromCodePoint(Math.abs(id1 + 2))
          getId = document.getElementById(id)
          getIdx2 = document.getElementById(idx2)
          if (getId && getId.children.length &&
            getId.firstChild.classList.contains(opponent) &&
            getIdx2 && !getIdx2.children.length) {
            getIdx2.classList.add('dropPos')
            getId.classList.add('kill-target')
          }
        }
      }
      if (e.classList.contains('damka-white') || e.classList.contains('damka-black')) {
        for (let i = 0; i < 4; i++) {
          if (i === 0) {
            damkaAttacks(a, b)
          } else if (i === 1) {
            a = -a
            damkaAttacks(a, b)
          } else if (i === 2) {
            b = -b
            damkaAttacks(a, b)
          } else {
            a = -a
            damkaAttacks(a, b)
          }
        }

        function damkaAttacks (id0, id1) {
          let counter = 1
          while (counter <= 8) {
            id = String.fromCodePoint(Math.abs(id0 + counter)) + String.fromCodePoint(Math.abs(id1 + counter))
            idx2 = String.fromCodePoint(Math.abs(id0 + counter + 1)) + String.fromCodePoint(Math.abs(id1 + counter + 1))
            getId = document.getElementById(id)
            getIdx2 = document.getElementById(idx2)
            if (getId && getId.children.length &&
              getId.firstChild.classList.contains(opponent) &&
              getIdx2 && !getIdx2.children.length) {
              getIdx2.classList.add('dropPos')
              getId.classList.add('kill-target')
              counter++
              while (counter <= 5) {
                idx2 = String.fromCodePoint(Math.abs(id0 + counter + 1)) + String.fromCodePoint(Math.abs(id1 + counter + 1))
                getIdx2 = document.getElementById(idx2)
                if (getIdx2 && !getIdx2.children.length) {
                  getIdx2.classList.add('dropPos')
                  counter++
                } else {
                  break
                }
              }
              break
            } else if (getId && !getId.children.length) {
              counter++
            } else {
              counter = 1
              break
            }
          }
        }
      }
    },
    checkCkeckers () {
      let opponentsСheckers
      let alienChecker
      let id
      let idx2
      let getId
      let getIdx2

      function checkItems (elem) {
        const id0 = elem.parentNode.id.codePointAt(0)
        const id1 = elem.parentNode.id.codePointAt(1)
        let a = id0
        let b = id1
        for (let i = 0; i < 4; i++) {
          if (i === 0) {
            if (checkForNextMove(a, b)) {
              return true
            }
          } else if (i === 1) {
            a = -a
            if (checkForNextMove(a, b)) {
              return true
            }
          } else if (i === 2) {
            b = -b
            if (checkForNextMove(a, b)) {
              return true
            }
          } else {
            a = -a
            if (checkForNextMove(a, b)) {
              return true
            }
          }
        }

        function checkForNextMove (id0, id1) {
          let counter = 1
          id = String.fromCodePoint(Math.abs(id0 + counter)) + String.fromCodePoint(Math.abs(id1 + counter))
          idx2 = String.fromCodePoint(Math.abs(id0 + counter + 1)) + String.fromCodePoint(Math.abs(id1 + counter + 1))
          getId = document.getElementById(id)
          getIdx2 = document.getElementById(idx2)
          if (elem.classList.contains('damka-white') || elem.classList.contains('damka-black')) {
            while (counter <= 8) {
              id = String.fromCodePoint(Math.abs(id0 + counter)) + String.fromCodePoint(Math.abs(id1 + counter))
              idx2 = String.fromCodePoint(Math.abs(id0 + counter + 1)) + String.fromCodePoint(Math.abs(id1 + counter + 1))
              getId = document.getElementById(id)
              getIdx2 = document.getElementById(idx2)
              if (getId && getId.children.length &&
                getId.firstChild.classList.contains(opponentsСheckers) &&
                getIdx2 && !getIdx2.children.length) {
                return true
              } else if (getId && !getId.children.length) {
                counter++
              } else {
                break
              }
            }
          } else {
            if (getId && getId.children.length &&
              getId.firstChild.classList.contains(opponentsСheckers) &&
              getIdx2 && !getIdx2.children.length) {
              return true
            }
          }
        }
      }

      let checkers
      let check
      if (this.flag === 'белые совершили ход') {
        opponentsСheckers = 'checker-white'
        alienChecker = '.checker-black'
        checkers = this.$el.querySelectorAll(alienChecker)
        checkers = Array.from(checkers)
        check = checkers.some(checkItems)
        if (check === true) {
          this.flag = 'удар черных'
        } else {
          this.flag = 'ход черных'
        }
      }
      if (this.flag === 'черные совершили ход') {
        opponentsСheckers = 'checker-black'
        alienChecker = '.checker-white'
        checkers = this.$el.querySelectorAll(alienChecker)
        checkers = Array.from(checkers)
        check = checkers.some(checkItems)
        if (check === true) {
          this.flag = 'удар белых'
        } else {
          this.flag = 'ход белых'
        }
      }
      if (this.flag === 'черные совершили удар' || this.flag === 'черные продолжайте бить') {
        opponentsСheckers = 'checker-white'
        alienChecker = '.last-move-black'
        checkers = this.$el.querySelectorAll(alienChecker)
        checkers = Array.from(checkers)
        check = checkers.some(checkItems)
        if (check === true) {
          this.flag = 'черные продолжайте бить'
        } else {
          opponentsСheckers = 'checker-black'
          alienChecker = '.checker-white'
          checkers = this.$el.querySelectorAll(alienChecker)
          checkers = Array.from(checkers)
          check = checkers.some(checkItems)
          if (check === true) {
            this.flag = 'удар белых'
          } else {
            this.flag = 'ход белых'
          }
        }
      }
      if (this.flag === 'белые совершили удар' || this.flag === 'белые продолжайте бить') {
        opponentsСheckers = 'checker-black'
        alienChecker = '.last-move-white'
        checkers = this.$el.querySelectorAll(alienChecker)
        checkers = Array.from(checkers)
        check = checkers.some(checkItems)
        if (check === true) {
          this.flag = 'белые продолжайте бить'
        } else {
          opponentsСheckers = 'checker-white'
          alienChecker = '.checker-black'
          checkers = this.$el.querySelectorAll(alienChecker)
          checkers = Array.from(checkers)
          check = checkers.some(checkItems)
          if (check === true) {
            this.flag = 'удар черных'
          } else {
            this.flag = 'ход черных'
          }
        }
      }
    },
    drop (event) {
      const x = document.querySelector('.take')
      if (event.target.classList.contains('dropPos')) {
        event.target.insertAdjacentElement('beforeend', x.firstChild)
        document.querySelectorAll('.last-move-black').forEach((e) => {
          e.classList.remove('last-move-black')
        })
        document.querySelectorAll('.last-move-white').forEach((e) => {
          e.classList.remove('last-move-white')
        })
        if (event.target.firstChild.classList.contains('checker-white')) {
          event.target.firstChild.classList.add('last-move-white')
          if (event.target.id === 'b8' || event.target.id === 'd8' ||
            event.target.id === 'f8' || event.target.id === 'h8') {
            event.target.firstChild.classList.add('damka-white')
          }
        } else {
          event.target.firstChild.classList.add('last-move-black')
          if (event.target.id === 'a1' || event.target.id === 'c1' ||
            event.target.id === 'e1' || event.target.id === 'g1') {
            event.target.firstChild.classList.add('damka-black')
          }
        }
        this.killItem(event)
        document.querySelectorAll('.take').forEach((e) => {
          e.classList.remove('take')
        })
        document.querySelectorAll('.dropPos').forEach((e) => {
          e.classList.remove('dropPos')
        })
        if (this.flag === 'ход белых') {
          this.flag = 'белые совершили ход'
        }
        if (this.flag === 'ход черных') {
          this.flag = 'черные совершили ход'
        }
        if (this.flag === 'удар черных') {
          this.flag = 'черные совершили удар'
        }
        if (this.flag === 'удар белых') {
          this.flag = 'белые совершили удар'
        }
        this.checkCkeckers()
        event.stopPropagation()
      }
    },
    checkDrop (event) {
      if (event.target.classList.contains('dropPos')) {
        this.drop(event)
      }
    },
    killItem (event) {
      const id0 = event.target.id.codePointAt(0)
      const id1 = event.target.id.codePointAt(1)
      let a = id0
      let b = id1
      for (let i = 0; i < 4; i++) {
        if (i === 0) {
          findKillTarget(a, b)
        } else if (i === 1) {
          a = -a
          findKillTarget(a, b)
        } else if (i === 2) {
          b = -b
          findKillTarget(a, b)
        } else {
          a = -a
          findKillTarget(a, b)
        }
      }

      function findKillTarget (id0, id1) {
        let counter = 1
        const idTake = document.querySelector('.take').id
        let id
        let getId
        const killTargets = []
        while (counter < 8) {
          id = String.fromCodePoint(Math.abs(id0 + counter)) + String.fromCodePoint(Math.abs(id1 + counter))
          getId = document.getElementById(id)
          if (getId && getId.id === idTake) {
            killTargets.forEach((e) => {
              e.innerHTML = ''
            })
            break
          } else if (getId) {
            killTargets.push(getId)
            counter++
          } else {
            break
          }
        }
      }

      document.querySelectorAll('.kill-target').forEach((e) => {
        e.classList.remove('kill-target')
      })
    },
    moveOrAttackBlack (event) {
      if (this.flag === 'ход черных') {
        return this.takeChecker(event)
      }
      if (this.flag === 'удар черных' || this.flag === 'черные продолжайте бить') {
        return this.attack(event)
      }
    },
    moveOrAttackWhite (event) {
      if (this.flag === 'ход белых') {
        return this.takeChecker(event)
      }
      if (this.flag === 'удар белых' || this.flag === 'белые продолжайте бить') {
        return this.attack(event)
      }
    }
  },
  mounted () {
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

  .take {
    background-color: gray;
    z-index: 3;
  }

  .kill-target {
    background-color: antiquewhite;
  }

  .last-move-white {
    background-color: aqua;
  }

  .last-move-black {
    background-color: #2c3e50;
  }

  .damka-white {
    background-color: chocolate;
  }

  .damka-black {
    background-color: chartreuse;
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
