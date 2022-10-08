<template>
    <v-card :disabled="isDisable" class="card" ref="card">
        <v-responsive v-if="width" :aspect-ratio="57/89">
            <div v-if="!isMiniCard" class="sub-text pa-0"
                :style="{ left: 0, top: 0, fontSize: `${width * .35}px`, transform: 'rotate(0)' }">{{ cardStr }}</div>
            <div class="main-text pa-0" :style="{ fontSize: `${width * .6}px` }">{{ isMiniCard ? cardStr : cardNum }}
            </div>
            <div v-if="!isMiniCard" class="sub-text pa-0"
                :style="{ left: '100%', top: '100%', fontSize: `${width * .35}px`, marginLeft: '-24px', marginTop: '-24px', transform: 'rotate(180deg)'  }">
                {{ cardStr }}</div>
        </v-responsive>
    </v-card>
</template>

<script>
export default {
    props: {
        number: Number,
        isDisable: Boolean,
    },
    data: () => {
        return {
            width: 0,
        }
    },
    computed: {
        isMiniCard() {
            return this.width < 15
        },
        cardStr() {
            return ({
                1: "A",
                10: "T",
                11: "J",
                12: "Q",
                13: "K",
                14: "?",
                15: "X",
            }[this.number]) || this.number.toString()
        },
        cardNum() {
            return ({
                14: "?",
                15: "X"
            })[this.number] || this.number.toString()
        },
    },
    mounted() {
        const resizeHandler = _ => {
            const cardElm = this.$refs.card && this.$refs.card.$el
            const width = cardElm && cardElm.clientWidth || 57

            this.width = width
        }

        resizeHandler()
        window.addEventListener("resize", resizeHandler)
    }
}
</script>

<style lang="scss" scoped>
.card {
    background-color: #fcfcfc;
}

.main-text {
    position: absolute;
    width: 88px;
    height: 88px;
    margin-left: -44px;
    margin-top: -44px;
    left: 50%;
    top: 50%;
    text-align: center;
    line-height: 88px;
    color: #666;
}

.sub-text {
    position: absolute;
    width: 24px;
    height: 24px;
    text-align: center;
    text-align: left;
    color: #999;
    user-select: none;
}

.card.v-card--disabled {
    background-color: #e0e0e0;
}
</style>
