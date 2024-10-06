<script lang="ts" setup>
type Balancer = {
    firstTokenName: string,
    secondTokenName: string,
    compareTokenName: string,
    firstTokenPrice: number,
    secondTokenPrice: number,
    firstTokenAmount: number,
    secondTokenAmount: number,
    startSum: number,
    transactionFee: number,
    deviationPercentage: number
}
let balancer = ref<Balancer>({
    firstTokenName: "",
    secondTokenName: "",
    compareTokenName: "",
    firstTokenPrice: 0,
    secondTokenPrice: 0,
    firstTokenAmount: 0,
    secondTokenAmount: 0,
    startSum: 0,
    transactionFee: 0,
    deviationPercentage: 0
})
let interval = 0.1
let cicle = 10

const results = ref([

])

let start = () => {
    results.value = []
    for (let i = 0; i < cicle; i++) {
        let tokenA = snapShot(Number(balancer.value.firstTokenPrice))
        let tokenB = snapShot(Number(balancer.value.secondTokenPrice))
        let different = Math.abs(tokenA - tokenB).toFixed(4)
        balancer.value.firstTokenPrice = tokenA
        balancer.value.secondTokenPrice = tokenB

        results.value.push({ tokenA: tokenA, tokenB: tokenB, different})
    }
}


let snapShot = (tokenPrice) => {
    return (Math.random() * ((tokenPrice + tokenPrice * interval) - (tokenPrice - tokenPrice * interval)) + (tokenPrice - tokenPrice * interval)).toFixed(4);
}


</script>

<template>
    <h3 class="text-center ma-8"> Исходные параметры</h3>
    {{ balancer }}
    <v-row>
        <v-col cols="6">
            <v-text-field label="Токена A" v-model="balancer.firstTokenName" variant="outlined"></v-text-field>
        </v-col>
        <v-col cols="6">
            <v-text-field label="Токен B" v-model="balancer.secondTokenName" variant="outlined"></v-text-field>
        </v-col>
        <v-col cols="6">
            <v-text-field label="Цена А" v-model="balancer.firstTokenPrice" type="number"
                variant="outlined"></v-text-field>
        </v-col>
        <v-col cols="6">
            <v-text-field label="Цена B" v-model="balancer.secondTokenPrice" type="number"
                variant="outlined"></v-text-field>
        </v-col>
        <v-col cols="6">
            {{ balancer.firstTokenPrice*balancer.firstTokenAmount }}
            <v-text-field label="Количество А" v-model="balancer.firstTokenAmount" type="number"
                variant="outlined"></v-text-field>
        </v-col>
        <v-col cols="6">
            {{ balancer.secondTokenPrice*balancer.secondTokenAmount }}
            <v-text-field label="Количество B" v-model="balancer.secondTokenAmount" type="number"
                variant="outlined"></v-text-field>
        </v-col>
        <v-col cols="6">
            <v-text-field label="Базовыый токен" v-model="balancer.compareTokenName" variant="outlined"></v-text-field>
        </v-col>
        <v-col cols="6">
            <v-text-field label="Стартовая сумма" v-model="balancer.startSum" type="number"
                variant="outlined"></v-text-field>
        </v-col>
        <v-col cols="6">
            <v-text-field label="Стоимость транзакции" v-model="balancer.transactionFee" type="number"
                variant="outlined"></v-text-field>
        </v-col>
        <v-col cols="6">
            <v-text-field label="Процент отклонения" v-model="balancer.deviationPercentage" type="number"
                variant="outlined"></v-text-field>
        </v-col>
    </v-row>
    <v-divider></v-divider>
    <p>
        Токен A: <b>{{ balancer.firstTokenName }}</b> | Токен B: <b>{{ balancer.secondTokenName }}</b> | Базовый токен:
        <b>{{ balancer.compareTokenName }}</b> | Стартовая сумма:
        <b>{{ balancer.startSum }}</b> | Стоимость транзакции:
        <b>{{ balancer.transactionFee }}</b> | Процент отклонения:
        <b>{{ balancer.deviationPercentage }}</b>
    </p>
    <v-divider></v-divider>
    <div class="d-flex justify-center ma-8"> <v-btn @click="start()">Посчитать</v-btn> </div>
    <v-divider></v-divider>
    <h3 class="text-center ma-8"> Результаты теста</h3>
    <v-data-table :items="results"></v-data-table>

</template>
<style></style>
