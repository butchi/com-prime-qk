<template>
  <v-row class="d-flex align-start">
    <v-col cols="12" md="8">
      <v-row no-gutters class="d-flex align-end mb-5">
        <v-col cols="12" sm="11">
          <v-row class="ma-0 pa-0">
            <v-col v-for="(num, pos) in handNumArr" :key="pos" :class="`${isXs ? 'mr-1' : 'ma-1'} pa-0`">
              <Card :isDisable="selectPositionArr.includes(pos)" :number="num"></Card>
            </v-col>
          </v-row>
        </v-col>
      </v-row>
      <v-row>
        <v-col class="ma-1 pa-3">
          <CardLine v-for="(cardLine, lineIdx) in cardLineArr.filter(cardLine => cardLine.cardNumSeq.length > 0)"
            :phase="phase" :cardNumSeq="cardLine.cardNumSeq" :cardNumSeqSucc="cardLine.cardNumSeqSucc"
            :isCurrent="lineIdx === curLineIdx" :isDisable="true" :isGrothen="cardLine.isGrothen" :key="lineIdx">
          </CardLine>
        </v-col>
      </v-row>
    </v-col>
  </v-row>
</template>

<script>
const numberFromArray = arr => {
  return parseInt(arr.map(item => item.toString()).join(""))
}

const primeQ = x => {
  if (x * 0 !== 0) {
    return false
  }
  if (x <= 3) {
    return x > 1
  }
  if (x % 2 === 0 || x % 3 === 0) {
    return false
  }
  for (let i = 5; i * i <= x; i += 6) {
    if (x % i === 0 || x % (i + 2) === 0) {
      return false
    }
  }
  return true
}

const getBlankCardLineArr = () => {
  return Array(11).fill(0).map(() => ({
    cardNumSeq: [],
    cardNumSeqSucc: [],
    isExact: false,
    isSuccess: false,
    isGrothen: false,
    isX3: false,
    isGrothen: false,
  }))
}

export default {
  name: 'IndexPage',
  data: () => {
    return {
      deckArr: [
        1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13,
        1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13,
        1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13,
        1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13,
        // 15, 15
      ],
      selectPositionArr: Array(11).fill(0).map((_, i) => i),
      curLineIdx: 0,
      handNumArr: Array(11).fill(14),
      cardLineArr: getBlankCardLineArr(),
      phase: "lobby",
      timer: null,
    }
  },
  computed: {
    isXs() {
      return this.$vuetify.breakpoint.xs
    },
  },
  mounted() {
    this.playDemo()
  },
  methods: {
    append(num, pos) {
      this.selectPositionArr.push(pos)

      this.cardLineArr[this.curLineIdx].cardNumSeq.push(num)
    },
    primeNumSucc({ cardLine }) {
      return numberFromArray(cardLine.cardNumSeqSucc)
    },
    playDemo() {
      this.reset()

      this.deckArr.sort(() => Math.random() - .5)

      this.handNumArr = this.deckArr.slice(0, 11)

      this.timer = setInterval(this.execTurn, 1999)
    },
    execTurn() {
      const cardPosArr = this.pickCard()

      if (cardPosArr.length === 0) {
        clearInterval(this.timer)

        this.playDemo()

        return
      }

      cardPosArr.forEach(cardPos => {
        this.selectCard(this.handNumArr[cardPos], cardPos)
      })

      setTimeout(_ => {
        this.curLineIdx++

        this.phase = "result"

        cardPosArr.forEach(cardPos => {
          this.handNumArr[cardPos] = Math.ceil(Math.random() * 13)
        })

        this.selectPositionArr.length = 0

        this.phase = "edit"
      }, 999)
    },
    pickCard() {
      let ret = []

      let lenPriority = 2

      const cardPosArr = []

      for (let i = 1; i <= 9; i += 2) {
        if (i === 5) {
          break
        }

        const tailOdd = i

        const tailOddPos = this.handNumArr.indexOf(tailOdd)

        if (lenPriority === 2 && tailOddPos !== -1) {
          for (let i = 0; i < this.handNumArr.length; i++) {
            const pos = i

            if (pos === tailOddPos) {
              break
            }

            const n = numberFromArray([
              this.handNumArr[pos],
              this.handNumArr[tailOddPos]
            ])

            if (primeQ(n)) {
              cardPosArr.push(pos)
              cardPosArr.push(tailOddPos)

              break
            }
          }
        }

        if (cardPosArr.length > 0) {
          break
        }
      }

      ret = cardPosArr

      return ret
    },
    reset() {
      this.phase = "edit"

      this.curLineIdx = 0

      this.selectPositionArr.length = 0

      this.cardLineArr = getBlankCardLineArr()
    },
    selectCard(num, pos) {
      if (this.selectPositionArr.includes(pos)) {
        return
      }

      this.append(num, pos)
    },
  },
}
</script>
