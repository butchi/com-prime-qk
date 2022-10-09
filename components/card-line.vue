<template>
    <v-card class="ma-1 pa-3">
        <v-row v-if="cardNumSeq.length > 0" class="d-flex align-end">
            <v-col v-for="(num, pos) in cardNumSeq" :key="pos" cols="1" class="ma-0 pa-1 py-1">
                <Card :isDisable="isDisable" :number="num"></Card>
            </v-col>
        </v-row>
    </v-card>
</template>

<script>
import '@mdi/font/css/materialdesignicons.css'

export default {
    props: {
        phase: String,
        cardNumSeq: Array,
        isCurrent: Boolean,
        isDisable: Boolean,
    },
    computed: {
    },
    methods: {
        clickDeleteButton() {
            console.log("delete")
        },
    }
}
</script>
