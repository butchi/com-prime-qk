<template>
    <v-card class="ma-1 pa-3">
        <v-row v-if="cardNumSeq.length > 0">
            <v-col cols="12" md="6">
                <v-row class="d-flex align-end">
                    <v-col v-for="(num, pos) in cardNumSeq" :key="pos" cols="1" class="ma-0 pa-1">
                        <Card :isDisable="isDisable" :number="num"></Card>
                    </v-col>
                </v-row>
            </v-col>
        </v-row>
        <v-row v-else-if="isCurrent">
            <v-col cols="12" md="6">
                <v-row>
                    <v-col cols="1" class="ma-0 pa-1">
                        <Card :isDisable="true" :number="14"></Card>
                    </v-col>
                </v-row>
            </v-col>
        </v-row>
        <v-row v-else>
            <v-col md="1" class="pa-0">
                <v-responsive :aspect-ratio="57/89" class="ma-0 pa-1"></v-responsive>
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
