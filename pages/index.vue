<template>
  <v-row class="d-flex align-start">
    <v-col cols="12" md="8">
      <v-row no-gutters class="d-flex align-end mb-5">
        <v-col cols="12" sm="11">
          <v-row class="ma-0 pa-0">
            <v-col
              v-for="(num, pos) in handNumArr"
              :key="pos"
              :class="`${isXs ? 'mr-1' : 'ma-1'} pa-0`"
              @click.stop="clickCard(num, pos)"
            >
              <Card
                :isDisable="selectPositionArr.includes(pos)"
                :number="num"
              ></Card>
            </v-col>
          </v-row>
        </v-col>
        <v-col cols="12" sm="1">
          <v-row :class="$vuetify.breakpoint.smAndUp ? 'ma-1 pa-0' : 'ma-1 pa-0 float-left'">
            <v-col class="ma-0 pa-0">
              <v-btn
                v-if="phase === 'edit' || phase === 'result'"
                :disabled="selectPositionArr.length === 0"
                color="primary"
                rounded
                plain
                @click.stop="reset"
              >
                <v-icon>
                  mdi-restore
                </v-icon>
              </v-btn>
              <v-btn
                v-if="phase === 'exec' && !isDisableReeval"
                color="warning"
                rounded
                plain
                @click.stop="clickReevaluate"
              >
                <v-icon>
                  mdi-calculator
                </v-icon>
              </v-btn>
            </v-col>
          </v-row>
          <v-row :class="$vuetify.breakpoint.smAndUp ? 'ma-1 pa-0' : 'ma-1 pa-0 float-right'">
            <v-col cols="2" sm="12" class="ma-0 pa-0">
              <v-btn
                v-if="phase === 'edit' && isAbleEnter"
                color="accent"
                rounded
                plain
                @click.stop="clickEnter"
              >
                <v-icon>
                  mdi-arrow-right-circle
                </v-icon>
              </v-btn>
              <v-btn
                v-else-if="phase === 'edit'"
                :disabled="cardLineArr[curLineIdx].cardPosSeq.length === 0"
                color="accent"
                rounded
                plain
                @click.stop="clickQumi"
              >
                <v-icon>
                  mdi-keyboard-return
                </v-icon>
              </v-btn>
              <v-btn
                v-else-if="phase === 'exec'"
                color="#eebc1d"
                rounded
                @click.stop="clickComplete"
              >
                <v-icon>
                  mdi-treasure-chest
                </v-icon>
              </v-btn>
              <v-btn
                  v-else-if="phase === 'result' || phase === 'lobby'"
                  color="primary"
                  rounded
                  @click.stop="play"
              >
                <v-icon>
                  mdi-cards-playing-outline
                </v-icon>
              </v-btn>
            </v-col>
          </v-row>
        </v-col>
      </v-row>
      <v-row>
        <v-col class="ma-1 pa-3">
          <CardLine
            v-for="(cardLine, lineIdx) in cardLineArr.filter(cardLine => cardLine.cardPosSeq.length > 0)"
            :phase="phase"
            :cardNumSeq="cardLine.cardPosSeq.map(pos => handNumArr[pos])"
            :cardNumSeqSucc="cardLine.cardNumSeqSucc"
            :isCurrent="lineIdx === curLineIdx"
            :isDisable="phase === 'exec' || phase === 'result' || lineIdx < curLineIdx"
            :isExact="cardLine.isExact"
            :isSuccess="cardLine.isSuccess"
            :isX3="cardLine.isX3"
            :isGrothen="cardLine.isGrothen"
            :key="lineIdx"
          ></CardLine>
        </v-col>
      </v-row>
      <v-snackbar
        v-model="snackbar"
        timeout="1500"
      >
        汲斬！
        <template v-slot:action="{ attrs }">
          <v-btn
            color="pink"
            text
            v-bind="attrs"
          >
            Close
          </v-btn>
        </template>
      </v-snackbar>
    </v-col>
    <v-col cols="0" md="4">
      <v-card>
        <v-card-title>
          出会いテム
        </v-card-title>
        <v-card-text>
          <v-row v-for="(item, idx) in [...new Set(deaitem)].reverse()" :key="idx">
            <v-col cols="12">
              <v-row>
                <v-col v-for="(num, i) in JSON.parse(item)" :key="i" cols="1" md="1" class="pa-1 mb-3">
                  <Card
                    :number="num"
                    :key="i"
                  ></Card>
                </v-col>
              </v-row>
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>
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
    cardPosSeq: [],
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
        15, 15
      ],
      deaitem: [],
      selectPositionArr: Array(11).fill(0).map((_, i) => i),
      curLineIdx: 0,
      handNumArr: Array(11).fill(14),
      cardLineArr: getBlankCardLineArr(),
      phase: "lobby",
      isAbleEnter: false,
      snackbar: false,
    }
  },
  computed: {
    isXs() {
      return this.$vuetify.breakpoint.xs
    },
    isAllExact() {
      return this.cardLineArr.filter(cardLine => cardLine.cardPosSeq.length > 0).every(cardLine => cardLine.isExact)
    },
    isDisableReeval() {
      return this.cardLineArr.filter(cardLine => cardLine.cardPosSeq.length > 0).every(cardLine => cardLine.isExact || (!this.handNumArr.includes(15) && cardLine.isX3) || cardLine.isGrothen)
    },
  },
  mounted() {
    document.body.addEventListener("keydown", evt => {
      if (evt.key === "Enter") {
        if (this.isAbleEnter) {
          this.clickEnter()
        } else if (this.phase === "result") {
          this.play()
        } else {
          this.clickQumi()
        }
      }

      if (evt.key === "Escape") {
        this.reset()
      }
    })
  },
  methods: {
    append(_num, pos) {
      this.selectPositionArr.push(pos)

      this.cardLineArr[this.curLineIdx].cardPosSeq.push(pos)
    },
    primeNumSucc({ cardLine }) {
      return numberFromArray(cardLine.cardNumSeqSucc)
    },
    shufflePrimeQ({ cardLine, shuffle = false }) {
      let ret = false

      let cardNumSeq = shuffle ? cardLine.cardPosSeq.map(pos => this.handNumArr[pos]).sort((a, b) => Math.random() - .5) : cardLine.cardPosSeq.map(pos => this.handNumArr[pos])

      const cardNumSeqReplJoker = cardNumSeq.map(item => ({
        "15" : Math.floor(Math.random() * 13),
      } [item] || item))

      const n = numberFromArray(cardNumSeqReplJoker)

      const isPrime = primeQ(n)

      if (isPrime || n === 57) {
        ret = true

        if (n > (this.primeNumSucc({ cardLine }) || 0)) {

          cardLine.cardNumSeqSucc = cardNumSeqReplJoker.slice(0)
        }

        if (n === 57) {
          cardLine.isGrothen = true
        } else {
          cardLine.isGrothen = false
        }
      } else if (n % 3 === 0) {
        cardLine.isX3 = true
      }

      return ret
    },
    exec() {
      this.cardLineArr.forEach(cardLine => {
        if (cardLine.cardPosSeq.length === 0) {
          return
        }

        const waitSec = .03
        const timestampOrig = Date.now()
        let timestamp = timestampOrig

        for (let i = 0; i < 99999; i++) {
          if (cardLine.isExact) {
            cardLine.isX3 = false

            return
          }

          timestamp = Date.now()

          if (timestamp > timestampOrig + waitSec * 1000) {
            break
          } else if (cardLine.cardPosSeq.length > 0) {
            cardLine.isExact = cardLine.isExact || this.shufflePrimeQ({ cardLine, shuffle: false })

            if (cardLine.isExact) {
              cardLine.isSuccess = cardLine.isExact
            } else {
              cardLine.isSuccess = cardLine.isSuccess || this.shufflePrimeQ({ cardLine, shuffle: true })
            }
          }

          if (cardLine.isSuccess) {
            cardLine.isX3 = false
          }

          if (cardLine.isExact) {
            break
          }
        }
      })

      if (this.cardLineArr.filter(cardLine => cardLine.cardPosSeq.length > 0).every(cardLine => cardLine.isSuccess)) {
        this.snackbar = true
      }
    },
    play() {
      this.reset()

      this.deckArr.sort(() => Math.random() - .5)

      this.handNumArr = this.deckArr.slice(0, 11).sort((a, b) => a - b)
    },
    reset() {
      this.phase = "edit"

      this.isAbleEnter = false

      this.curLineIdx = 0

      this.selectPositionArr.length = 0

      this.cardLineArr = getBlankCardLineArr()
    },
    clickQumi() {
      if (this.cardLineArr[this.curLineIdx].cardPosSeq.length > 0) {
        this.curLineIdx++
      }
    },
    clickEnter() {
      this.phase = "exec"

      this.exec()
    },
    clickReevaluate() {
      this.exec()
    },
    clickCard(num, pos) {
      if (this.selectPositionArr.includes(pos)) return

      this.append(num, pos)

      if (this.selectPositionArr.length >= 11) {
        this.isAbleEnter = true
      }
    },
    clickComplete() {
      this.phase = "result"

      this.cardLineArr.map(cardLine => cardLine.cardNumSeqSucc).filter(seq => seq.length > 0).forEach(cardNumSeq => {
        this.deaitem.push(JSON.stringify(cardNumSeq))
      })
    },
  },
}
</script>
