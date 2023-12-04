<script setup>
import { ref, onMounted, watch } from "vue"
import { db } from "./data/guitar"
import GuitarsC from "./components/Guitars.vue";
import Header from "./components/Header.vue"
import Foother from "./components/Foother.vue";

const guitars = ref([])
const buy = ref([])
const guitar = ref({})
watch(buy, () => {
    addLocalStorage()
}, {
    deep:true
})

onMounted(() => {
    guitars.value = db
    guitar.value = db[3]

    const buyStorage = localStorage.getItem('buy')
    buyStorage ? buy.value = JSON.parse(buyStorage) : console.log('vacio')
})

const addLocalStorage = () => {
   localStorage.setItem('buy',JSON.stringify(buy.value))
}

const addBuy = (guitar) => {
    const existElement = buy.value.findIndex(product=> product.id===guitar.id)

    if (existElement >= 0) {
        buy.value[existElement].count++
    } else {

        guitar.count = 1
        buy.value.push(guitar)
    }
   
}

const increment = (id) => {
    const index = buy.value.findIndex(product => product.id === id)
     buy.value[index].count >= 10 ? buy.value[index].count : buy.value[index].count++
     

}

const decrement = (id) => {
    const index = buy.value.findIndex(product => product.id === id)
    buy.value[index].count<=1? buy.value[index].count: buy.value[index].count--
}

const deleted = (id) => {
  buy.value=buy.value.filter(elem=>elem.id!==id)
}
const deletedAll = (buy) => {
    buy.splice(0)
}


</script>

<template>
  
<Header
:buy="buy"
:guitar="guitar"
@increment="increment"
@decrement="decrement"
      @add-buy="addBuy"
      @deleted="deleted"
      @deleted-all="deletedAll"
/>
     <main class="container-xl mt-5">
        <h2 class="text-center text-custom">Nuestra Colección</h2>
      <div class="row mt-5 ">
        <GuitarsC
            v-for="guitar in guitars"
            :key="guitar.id"
            :guitar="guitar"
            @add-buy="addBuy"
            class="shop-guitar"
            
        />
    </div>
</main>
<Foother/>
   
</template>

