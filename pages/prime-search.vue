<template>
    <v-col>
        <v-row>
            <v-col>
                <v-text-field v-model="searchTxt" append-outer-icon="mdi-magnify" label="Search" single-line
                    hide-details :rules="[value => /^[0-9TJQK]+$/.test(value) || 'Invalid sequence.']"
                    @click:append-outer="search(searchTxt)">
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
                <v-text-field v-model="searchTxt" append-outer-icon="mdi-magnify" label="Search" single-line
                    hide-details class="ma-5" @click:append-outer="search(searchTxt)">
                </v-text-field>
                <v-row class="justify-center my-5">
                    <v-col cols="10" md="3">
                        <v-row v-for="y in [0, 1, 2]" :key="y">
                            <v-col v-for="x in [0, 1, 2, 3, 4]" :key="x" class="pa-1"
                                @click="searchTxt += { 10: 'T', 11: 'J', 12: 'Q', 13: 'K', 14: 'X' }[x + y * 5] || x + y * 5">
                                <Card :is-disable="x + y * 5 > 13" :number=" x + y * 5 === 14 ? Infinity : x + y * 5">
                                </Card>
                            </v-col>
                        </v-row>
                    </v-col>
                </v-row>
            </v-sheet>
        </v-bottom-sheet>
        <v-bottom-sheet v-model="numberSheet">
            <v-sheet height="380px" color="grey lighten-1">
                <v-text-field v-model="searchTxt" append-outer-icon="mdi-magnify" label="Search" single-line
                    hide-details class="ma-5" @click:append-outer="search(searchTxt)">
                </v-text-field>
                <v-row class="justify-center my-5">
                    <v-col cols="10" md="3">
                        <v-row v-for="y in [0, 1, 2]" :key="y" class="text-center">
                            <v-col v-for="x in [0, 1, 2]" :key="x" class="pa-1" @click="searchTxt += x + y * 3 + 1">
                                <v-btn fab>{{ x + y * 3 + 1 }}</v-btn>
                            </v-col>
                        </v-row>
                        <v-row class="text-center">
                            <v-col class="pa-1" @click="searchTxt += X">
                                <v-btn fab disabled>X</v-btn>
                            </v-col>
                            <v-col class="pa-1" @click="searchTxt += '0'">
                                <v-btn fab>0</v-btn>
                            </v-col>
                            <v-col class="pa-1" @click="searchTxt = searchTxt.slice(0, -1)">
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

const parseQkText = txt => {
    const txtRepl = txt.toUpperCase()
        .replace("A", "1")
        .replace("T", "10")
        .replace("J", "11")
        .replace("Q", "12")
        .replace("K", "13")

    const ret = BigInt(txtRepl)

    return ret
}

export default {
    name: "PrimeSearch",
    data() {
        return {
            cardSheet: false,
            numberSheet: false,
            searchTxt: "",
            result: null,
        };
    },
    methods: {
        search(txt) {
            const num = parseQkText(txt)

            const isPrime = primeQ(num)
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
  