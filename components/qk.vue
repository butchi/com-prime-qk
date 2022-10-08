<template>
    <v-col>
        <v-row no-gutters class="d-flex align-end mb-5">
            <v-col v-for="(num, pos) in handNumArr" :key="pos" :class="`${isXs ? 'mr-1' : ''} ma-0 pa-1`" cols="1">
                <Card :isDisable="selectPositionArr.includes(pos)" :number="num"></Card>
            </v-col>
        </v-row>
        <v-row>
            <v-col class="ma-1 pa-3">
                <CardLine v-for="(cardLine, lineIdx) in cardLineArr.slice(0, -1).reverse()" :phase="phase"
                    :cardNumSeq="cardLine.cardNumSeq" :cardNumSeqSucc="cardLine.cardNumSeqSucc"
                    :isCurrent="lineIdx === curLineIdx" :isDisable="true" :isGrothen="cardLine.isGrothen"
                    :key="lineIdx">
                </CardLine>
            </v-col>
        </v-row>
    </v-col>
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
            cardLineArr: [{
                cardNumSeq: [],
                cardNumSeqSucc: [],
                isExact: false,
                isSuccess: false,
                isGrothen: false,
                isX3: false,
                isGrothen: false,
            }],
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

            this.cardLineArr.at(-1).cardNumSeq.push(num)
        },
        primeNumSucc({ cardLine }) {
            return numberFromArray(cardLine.cardNumSeqSucc)
        },
        playDemo() {
            this.reset()

            this.deckArr.sort(() => Math.random() - .5)

            this.handNumArr = this.deckArr.slice(0, 11)

            this.execTurn()
        },
        async execTurn() {
            const cardPosArr = await this.pickCard()

            await new Promise(resolve => setTimeout(resolve, 300))

            if (cardPosArr.length === 0) {
                clearInterval(this.timer)

                this.playDemo()

                return
            }

            cardPosArr.forEach(async (cardPos, idx) => {
                setTimeout(_ => {
                    this.selectCard(this.handNumArr[cardPos], cardPos)
                }, idx * 300)
            })

            await new Promise(resolve => setTimeout(resolve, cardPosArr.length * 300 + 300))

            this.cardLineArr.push({
                cardNumSeq: [],
                cardNumSeqSucc: [],
                isExact: false,
                isSuccess: false,
                isGrothen: false,
                isX3: false,
                isGrothen: false,
            })

            this.phase = "result"

            cardPosArr.forEach(cardPos => {
                // this.handNumArr[cardPos] = 2 * Math.ceil(Math.random() * 6)
                this.handNumArr[cardPos] = Math.ceil(Math.random() * 13)
            })
            this.handNumArr.length = 11

            this.selectPositionArr.length = 0

            this.phase = "edit"

            await new Promise(resolve => setTimeout(resolve, 999))

            this.execTurn()
        },
        pickCard() {
            let ret = []

            let lenPriorityArr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11].sort(_ => Math.random() - .5)

            let cardPosSeq = []

            const findPrimeCardPosSeq = numSeq => {
                let ret = []

                let randomPosSeq = []

                for (let lenPriorityIdx = 0; lenPriorityIdx < lenPriorityArr.length; lenPriorityIdx++) {
                    const lenPriority = lenPriorityArr[lenPriorityIdx]

                    randomPosSeq = (new Array(numSeq.length)).fill(0).map((_, i) => i).slice(0).sort(_ => Math.random() - .5).slice(0, lenPriority)

                    if (lenPriority > 1 && ![1, 3, 7, 9, 11, 13].includes(numSeq[randomPosSeq.at(-1)])) {
                        continue
                    }
                }

                const n = numberFromArray(
                    randomPosSeq.map(pos => numSeq[pos])
                )

                if (primeQ(n)) {
                    ret = randomPosSeq
                }

                return ret
            }

            for (let i = 0; i <= 9999; i++) {
                cardPosSeq = findPrimeCardPosSeq(this.handNumArr)

                if (cardPosSeq.length > 0) {
                    ret = cardPosSeq

                    return ret
                }
            }

            const helpCardNumArr = [1, 3, 7, 9]

            for (let i = 0; i <= 9999; i++) {
                for (let helpCardNumIdx = 0; helpCardNumIdx < helpCardNumArr.length; helpCardNumIdx++) {
                    const helpCardNum = helpCardNumArr[helpCardNumIdx]

                    cardPosSeq = findPrimeCardPosSeq([
                        this.handNumArr.slice(0),
                        [helpCardNum],
                    ].flat())

                    if (cardPosSeq.length > 0) {
                        this.handNumArr.push(helpCardNum)

                        ret = cardPosSeq

                        return ret
                    }
                }
            }

            ret = cardPosSeq

            return ret
        },
        reset() {
            this.phase = "edit"

            this.curLineIdx = 0

            this.selectPositionArr.length = 0

            this.cardLineArr = [{
                cardNumSeq: [],
                cardNumSeqSucc: [],
                isExact: false,
                isSuccess: false,
                isGrothen: false,
                isX3: false,
                isGrothen: false,
            }]
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
  