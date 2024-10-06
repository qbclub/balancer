<script lang="ts" setup>
type Balancer = {
    AName: string,
    BName: string,
    compareTokenName: string,
    APrice: number,
    BPrice: number,
    AAmount: number,
    BAmount: number,
    startSum: number,
    transactionFee: number,
    deviationPercentage: number
}
let balancer = ref<Balancer>({
    AName: "",
    BName: "",
    compareTokenName: "",
    APrice: 0,
    BPrice: 0,
    AAmount: 0,
    BAmount: 0,
    startSum: 0,
    transactionFee: 0,
    deviationPercentage: 0
})
let interval = 0.05
let cicle = 3

const results = ref([

])


let headers = ref([
    {
        title: 'A',
        align: 'center',
        children: [
            { title: 'Цена', value: 'APrice' },
            { title: 'Количество', value: 'AAmount' },
            { title: 'Сумма', value: 'ASum' },
            {
                title: "Обмен", value: "AChange"
            }
        ],
    },
    {
        title: 'B',
        align: 'center',
        children: [
            { title: 'Цена', value: 'BPrice' },
            { title: 'Количество', value: 'BAmount' },
            { title: 'Сумма', value: 'BSum' },
            {
                title: "Обмен", value: "BChange"
            }
        ],
    },
    { title: 'Расхождение', value: 'different' },
    { title: 'Всего', value: 'total' }
]
)

let start = () => {
    results.value = []
    for (let i = 0; i < cicle; i++) {
        let total = balancer.value.AAmount * balancer.value.APrice + balancer.value.BPrice * balancer.value.BAmount
        let APrice = snapShot(Number(balancer.value.APrice))
        let BPrice = snapShot(Number(balancer.value.BPrice))
        balancer.value.APrice = APrice
        balancer.value.BPrice = BPrice
        let AAmount = balancer.value.AAmount
        let BAmount = balancer.value.BAmount


        let ASum = (APrice * balancer.value.AAmount).toFixed(4)
        let BSum = (BPrice * balancer.value.BAmount).toFixed(4)

        let different = (ASum - BSum).toFixed(4)
        let { AChange, BChange } = change(different)
        results.value.push({ APrice, AAmount, ASum, AChange, BPrice, BAmount, BSum, BChange, different, total })
    }
}


let snapShot = (tokenPrice) => {
    return (Math.random() * ((tokenPrice + tokenPrice * interval) - (tokenPrice - tokenPrice * interval)) + (tokenPrice - tokenPrice * interval)).toFixed(4);
}
let sumTokens = (price, amount) => {
    return (price * amount).toFixed(4)
}
let change = (diff) => {
    let AChange, BChange = 0
    if (diff == 0) {
        return
    }
    AChange = ((diff / 2) / balancer.value.APrice).toFixed(2)
    BChange = - ((diff / 2) / balancer.value.BPrice).toFixed(2)
    balancer.value.AAmount = Number(balancer.value.AAmount) + Number(AChange)
    balancer.value.BAmount = Number(balancer.value.BAmount) - Number(BChange)

    return { AChange,  BChange }
}

watch(() => balancer.value.startSum, () => {
    balancer.value.AAmount = (balancer.value.startSum / balancer.value.APrice).toFixed(4)
    balancer.value.BAmount = (balancer.value.startSum / balancer.value.BPrice).toFixed(4)
})
</script>

<template>
    <h3 class="text-center ma-8"> Исходные параметры</h3>

    <v-row>
        <v-col cols="6">
            <v-text-field label="Токена A" v-model="balancer.AName" variant="outlined"></v-text-field>
        </v-col>
        <v-col cols="6">
            <v-text-field label="Токен B" v-model="balancer.BName" variant="outlined"></v-text-field>
        </v-col>
        <v-col cols="6">
            <v-text-field label="Цена А" v-model="balancer.APrice" type="number" variant="outlined"></v-text-field>
        </v-col>
        <v-col cols="6">
            <v-text-field label="Цена B" v-model="balancer.BPrice" type="number" variant="outlined"></v-text-field>
        </v-col>
        <v-col cols="6">
            {{ balancer.APrice * balancer.AAmount }}
            <v-text-field label="Количество А" v-model="balancer.AAmount" type="number"
                variant="outlined"></v-text-field>
        </v-col>
        <v-col cols="6">
            {{ balancer.BPrice * balancer.BAmount }}
            <v-text-field label="Количество B" v-model="balancer.BAmount" type="number"
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
        Токен A: <b>{{ balancer.AName }}</b> | Токен B: <b>{{ balancer.BName }}</b> | Базовый токен:
        <b>{{ balancer.compareTokenName }}</b> | Стартовая сумма:
        <b>{{ balancer.startSum }}</b> | Стоимость транзакции:
        <b>{{ balancer.transactionFee }}</b> | Процент отклонения:
        <b>{{ balancer.deviationPercentage }}</b>
    </p>
    <v-divider></v-divider>
    <div class="d-flex justify-center ma-8"> <v-btn @click="start()">Посчитать</v-btn> </div>
    <v-divider></v-divider>
    <h3 class="text-center ma-8"> Результаты теста</h3>
    <v-data-table :headers="headers" :items="results" item-key="name"></v-data-table>

</template>
<style></style>
