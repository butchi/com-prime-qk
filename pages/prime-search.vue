<template>
    <v-col>
        <v-row>
            <v-col>
                <v-text-field v-model="strSearch" append-outer-icon="mdi-magnify" label="Search" single-line
                    hide-details :rules="[validQkStringQ || 'Invalid sequence.']"
                    @click:append-outer="search(strSearch)" @keydown.enter="search(strSearch)">
                </v-text-field>
            </v-col>
        </v-row>
        <v-row>
            <v-col>
                <v-card v-if="result">
                    <v-card-title>計算結果</v-card-title>

                    <v-card-text>
                        {{ result.number }}は{{ result.isPrime ? "素数です！" : "素数ではありません！" }}
                    </v-card-text>
                </v-card>
            </v-col>
        </v-row>
        <v-row>
            <v-col cols="2">
                <v-btn fab absolute bottom right color="primary" @click="numberSheet = !numberSheet" class="mr-15">
                    <v-icon>mdi-calculator</v-icon>
                </v-btn>
            </v-col>
            <v-col cols="2">
                <v-btn fab absolute bottom right color="primary" @click="cardSheet = !cardSheet">
                    <v-icon>mdi-cards</v-icon>
                </v-btn>
            </v-col>
        </v-row>

        <v-bottom-sheet v-model="cardSheet" dark>
            <v-sheet height="450px" color="grey lighten-1">
                <v-text-field v-model="strSearch" append-outer-icon="mdi-magnify" label="Search" single-line
                    hide-details class="ma-5" @click:append-outer="search(strSearch)">
                </v-text-field>
                <v-row class="justify-center my-5">
                    <v-col cols="10" md="3">
                        <v-row v-for="y in [0, 1, 2]" :key="y">
                            <v-col v-for="x in [0, 1, 2, 3, 4]" :key="x" class="pa-1"
                                @click="strSearch += { 10: 'T', 11: 'J', 12: 'Q', 13: 'K', 14: 'X' }[x + y * 5] || x + y * 5">
                                <Card :is-disable="x + y * 5 > 13" :number="x + y * 5 === 14 ? Infinity : x + y * 5">
                                </Card>
                            </v-col>
                        </v-row>
                    </v-col>
                </v-row>
            </v-sheet>
        </v-bottom-sheet>
        <v-bottom-sheet v-model="numberSheet">
            <v-sheet height="380px" color="grey lighten-1">
                <v-text-field v-model="strSearch" append-outer-icon="mdi-magnify" label="Search" single-line
                    hide-details class="ma-5" @click:append-outer="search(strSearch)">
                </v-text-field>
                <v-row class="justify-center my-5">
                    <v-col cols="10" md="3">
                        <v-row v-for="y in [0, 1, 2]" :key="y" class="text-center">
                            <v-col v-for="x in [0, 1, 2]" :key="x" class="pa-1" @click="strSearch += x + y * 3 + 1">
                                <v-btn fab>{{ x + y * 3 + 1 }}</v-btn>
                            </v-col>
                        </v-row>
                        <v-row class="text-center">
                            <v-col class="pa-1" @click="strSearch += X">
                                <v-btn fab disabled>X</v-btn>
                            </v-col>
                            <v-col class="pa-1" @click="strSearch += '0'">
                                <v-btn fab>0</v-btn>
                            </v-col>
                            <v-col class="pa-1" @click="strSearch = strSearch.slice(0, -1)">
                                <v-btn fab>
                                    <v-icon small>mdi-backspace</v-icon>
                                </v-btn>
                            </v-col>
                        </v-row>
                    </v-col>
                </v-row>
            </v-sheet>
        </v-bottom-sheet>
    </v-col>
</template>

<script>
import Card from '~/components/card.vue'

// [How to Find Very Large Prime Numbers in JavaScript | by Jeff Lowery | JavaScript in Plain English](https://javascript.plainenglish.io/how-to-find-very-large-prime-numbers-in-javascript-5a563ba2f3bb)
// [Miller-Rabin test using BigInt](https://gist.github.com/JeffML/424a448f1c10d85e10de000420fa1b8d#file-millerrabintest-js)

const power = (x, y, p) => {
    let res = 1n

    x = x % p

    while (y > 0n) {
        if (y & 1n) {
            res = (res * x) % p
        }

        y = y / 2n
        x = (x * x) % p
    }

    return res
}

const miillerTest = (d, n) => {
    const r = BigInt(Math.floor(Math.random() * 100000))
    const y = r * (n - 2n) / 100000n
    let a = 2n + y % (n - 4n)

    let x = power(a, d, n)

    if (x == 1n || x == n - 1n) {
        return true
    }

    while (d != n - 1n) {
        x = (x * x) % n
        d *= 2n

        if (x == 1n) {
            return false
        }
        if (x == n - 1n) {
            return true
        }
    }

    return false
}

function probablyPrimeQ(n, k = 40) {
    if (n <= 1n || n == 4n) return false
    if (n <= 3n) return true

    let d = n - 1n
    while (d % 2n == 0n) {
        d /= 2n
    }

    for (let i = 0; i < k; i++) {
        if (!miillerTest(d, n)) {
            return false
        }
    }

    return true
}

const primeQ = x => {
    if (x * 0n !== 0n) {
        return false
    }
    if (x <= 3n) {
        return x > 1n
    }
    if (x % 2n === 0 || x % 3n === 0) {
        return false
    }
    for (let i = 5n; i * i <= x; i += 6n) {
        if (x % i === 0n || x % (i + 2n) === 0n) {
            return false
        }
    }
    return true
}

export default {
    name: "PrimeSearch",
    data() {
        return {
            cardSheet: false,
            numberSheet: false,
            strSearch: "",
            result: null,
        };
    },
    methods: {
        validQkStringQ(val) {
            return /^[0-9ATJQKatjqk]+$/.test(val)
        },
        parseQkString(str) {
            if (!this.validQkStringQ(str)) {
                return NaN
            }

            const strRepl = str.toUpperCase()
                .replaceAll("A", "1")
                .replaceAll("T", "10")
                .replaceAll("J", "11")
                .replaceAll("Q", "12")
                .replaceAll("K", "13")

            const ret = BigInt(strRepl)

            return ret
        },
        search(str) {
            const num = this.parseQkString(str)

            if (Number.isNaN(num)) {
                return
            }

            const isPrime = probablyPrimeQ(num)
            const isX2 = (num > 2n) && (num % 2n)
            const isX3 = (num > 3n) && (num % 3n)
            const isX5 = (num > 5n) && (num % 5n)

            this.result = {
                number: num,
                isPrime,
                isX2,
                isX3,
                isX5,
            }

            this.cardSheet = false
            this.numberSheet = false

            return
        },
    },
    components: { Card }
}
</script>
  