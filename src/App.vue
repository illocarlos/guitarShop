<script setup>
// llamamos metodos de vue 
import { ref, onMounted, watch, reactive } from "vue"
import { db } from "./data/guitar"


// importamos componentes que creamos 
import GuitarsC from "./components/Guitars.vue";
import Header from "./components/Header.vue"
import Foother from "./components/Foother.vue";


// son declaraciones reactivas y su sintaxis es con ref para que sean reactiva
//!como norma ref se usa para todo menos para objeto que se 
// usa reactive aunque aqui usamos ref para guitar
//si usamos ref al querer llamarla para otra funcion debemos poner .value para entrar en el valor de 
//el dato reactivo
const guitars = ref([])
const buy = ref([])
const guitar = ref({})
const countGuitar = ref(0)

// los watch son fuinciones que van a escuchar cuando el objeto o dato reactivo que le pasamos
// en este caso es buy cambien en la aplicacion cada vez que usemos un evento o funcion 
// watch estara observando si cambia 
watch(buy, () => {
    // le pasamos la funcion que queremops usar para no colocarla en todo los eventos o funciones
    //en este caso con watch llamando buy estamos constantemente viendo los cambios de este
    //y se lo pasamos al localstorage
    addLocalStorage()
}, {
    //deep observa  cuando cambia el objeto y sus cantidades
    deep:true
})



// aqui montamos los datos reactivos dandole valor y activandolos en toda la aplicacion
// normalmente se usa para base de dato o llamada de api con axio o fetch
//es decir damos valores a los datos mas importantes de la aplicacion para que esten montados 
//siempre estara activo una vez se active el ciclo de vida de la web
onMounted(() => {
    guitars.value = db
    guitar.value = db[3]

    const buyStorage = localStorage.getItem('buy')
    const buycount = localStorage.getItem('countGuitar')

      buycount ? countGuitar.value = JSON.parse(buycount) : console.log('vacio')
    buyStorage ? buy.value = JSON.parse(buyStorage) : console.log('vacio')
})

// normalmente ponemos los eventos o funciones mas abajo esto se llaman con un @

// agregamos una pequeña memoria usando el local storage para guardar la info de la tienda
const addLocalStorage = () => {
    //no hay que explicar nada son lentejas esto es para guardar en memoria algo de manera persistente
    localStorage.setItem('countGuitar', JSON.stringify(countGuitar.value))
   localStorage.setItem('buy',JSON.stringify(buy.value))
}

// agregamos a la tienda las guitarras que queremos meter y le pasamos guitar ya que viene de un componente hijo 
const addBuy = (guitar) => {
    //usamos el metodo findIndex en buy y con findIndex buscamos de los productos que tiene dentro buy
    //uno que tenga la  misma id que el objeto que le pasamos en este caso guitar.id
    //si la id del producto es igual el id de la guitarra lo almacenara
    const existElement = buy.value.findIndex(product=> product.id===guitar.id)
// si el existelement es mayor o igual a 0 significa que encontro un elemento 
    // en ese caso aumenta el contador en 1 que es el que usamos para poner las cantidades

    if (existElement >= 0) {
        buy.value[existElement].count++
    } else {
        // en caso de que no encuentre ninguno esto hace que es una nueva guitarra y empieza en uno en vez de -1
// y se agregara la guitarra el buy con el punto push
        guitar.count = 1
        buy.value.push(guitar)
    }
    incrementBuy()
   
}

// igualmente  en incremento y decremento lo usamos para contar las guitarras y debemos pasarle las id de las guitarras
// para contarlas y pusimos un tope de max 10 y min 1 
const increment = (id) => {
    console.log('---------->', countGuitar.value)
    const index = buy.value.findIndex(product => product.id === id)
     buy.value[index].count >= 10 ? buy.value[index].count : buy.value[index].count++
     incrementBuy()
}

const decrement = (id) => {
    //explicando el decrement, entendemos en increment 
    //
    const index = buy.value.findIndex(product => product.id === id)
    buy.value[index].count <= 1 ? buy.value[index].count : buy.value[index].count--
    decrementBuy()
}

// esta funcion es para borrar un conjunto de guitarras sin eliminar todo el carrito 
// debemos pasarle la id de cada elemento dentro que viene como prop
const deleted = (id) => {
    // usamos filter y filtramos la tienda y le preguntamos si la id de ese elemento 
    // es difernte que la id que le pasamos y si es diferente se quedan en la tienda y si son iguales se eliminan
    
  buy.value=buy.value.filter(elem=>elem.id!==id)
}
// aqui dejamos a 0 el carrito eliminando todo lo que tiene dentro
// le pasamos la buy que viene como prop del componente hijo modificiada
// y le hacemos un splice desde la pos 0 y borramos todo desde la 0
const deletedAll = (buy) => {
    buy.splice(0)
    countGuitar.value=0
}

const incrementBuy = () => {
      const totalCount = buy.value.reduce((total, product) => total + (product.count || 0), 0);
    countGuitar.value = totalCount
}

const decrementBuy = () => {
    const totalCount = buy.value.reduce((total, product) => total - (product.count || 0), 0);

    if (totalCount < 0) {
        countGuitar.value =Math.abs(totalCount)
    }
}


</script>

<template>
  <!-- llamamos al componente header y le pasamos los eventos y las props -->
  <!-- props se llaman con -> :prop="prop" -->
<!-- evento se llama-> @evento=evento ,normalmente los evento se les pasa a los hijos como @event-to -->
<Header
:buy="buy"
:guitar="guitar"
:countGuitar="countGuitar"
@increment="increment"
@decrement="decrement"
      @add-buy="addBuy"
      @deleted="deleted"
      @deleted-all="deletedAll"
      @increment-buy="incrementBuy"
/>
     <main class="container-xl mt-5">
        <h2 class="text-center text-custom">Nuestra Colección</h2>
      <div class="row mt-5 ">
        <!-- llamamos al componente GuitarsC -->
        <!-- y aqui traemos la bd y le hacemos un for para iterar en ella por cada objeto y lo reflejamos -->
        <!-- v-for tiene que ir siempre con :key -->
        <!-- le pasamos la props guitar -->
        <!-- y el evento addBuy -->
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

