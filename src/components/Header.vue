     
<script setup>
import { computed } from 'vue'
// definimos las props tal que asi y debemos usar el type de el que sea esa prop que este en el padre
//se suele poner arriba de los script
const props = defineProps({
    buy: {
        type: Array,
        required:true
    },
    guitar: {
        type: Object,
        required:true
    },
    countGuitar: {
        type: Number,
        required:true,
    }
})
// con defineEmits llamamos eventos pasados por el padre y
//  debemos activarlos con @click="$emit(nombreevento,->le pasamos una prop para usarla en dicho evento <-)"
defineEmits(['increment', 'decrement', 'add-buy', 'deleted', 'deleted-all','increment-buy' ]) 

// defineEmit y defineProp son palabra reservada de vue pero no son importadas como la mayoria


// para usar computed es igual que un evento o funcion normal pero hay que rodear toda la funcion con la palabra
// computed y coge un call back
//siempre usamos al final un return por que es lo que se va a monstrar en pantalla
//este computed lo usamos para calcular el total de todo los propductos que queremos comprar
//
const total = computed(() => {
    //usamos buy o carrito que es lo que vamos a iterar y debemos llamarlo con prop ya que no esta declarado aqui
    //si no en el componente padre
    // usamor reduces un array metodo que obligatoriamente tiene que coger dos parametros(total y elem)
    //aqui usamos un total que lo iniciamos en 0 como aparece al final del metodo
    //y un elem que es cada elemento del carrito (buy)
    //la logica es total es 0 de primera y  vamos contando la cantidad de elementos por el precio 
    //y se la sumamos al total tantas veces como elementos tengamos
    return props.buy.reduce((total, elem) =>  total + (elem.count * elem.precio),0)
     })
     
</script>


<template>
    <header class="py-5 header">
              <div class="container-xl">
                  <div class="row justify-content-center justify-content-md-between">
                      <div class="col-8 col-md-3">
                          <a href="index.html">
                              <img class="img-fluid" src="/img/logo.svg" alt="imagen logo">
                          </a>
                      </div>
                      <nav class="col-md-6 a mt-5 d-flex align-items-start justify-content-end">
                        <div class="divCoun" >
                            <p>{{ countGuitar }}</p>
                        </div>
                          <div 
                              class="carrito"
                          >
                              <img class="img-fluid" src="/img/carrito.png" alt="imagen carrito" />
                     
                              <div id="carrito" class="bg-white p-3">
                                  <p v-if="buy.length===0" class="text-center">
                                    El carrito esta vacio
                                </p>
                                <div v-else>
                                  <table  class="w-100 table">
                                      <thead>
                                          <tr>
                                              <th>Imagen</th>
                                              <th>Nombre</th>
                                              <th>Precio</th>
                                              <th>Cantidad</th>
                                              <th></th>
                                          </tr>
                                      </thead>
                                      <tbody>
                                          <tr 
                                          v-for="element in buy"
                                          :key="element.id"
                                          >
                                              <td>
                                                  <img class="img-fluid" 
                                                 :src="'/img/' + element.imagen + '.jpg'" 
                                                  :alt=" element.nombre ">
                                              </td>
                                              <td>{{ element.nombre }}</td>
                                              <td class="fw-bold">
                                                      {{ element.precio }}
                                              </td>
                                              <td class="flex align-items-start gap-4">
                                                  <button
                                                      type="button"
                                                      class="btn btn-dark"
                                                      @click="$emit('decrement',element.id)"
                                                  >
                                                      -
                                                  </button>
                                                      {{ element.count }}
                                                  <button
                                                      type="button"
                                                      class="btn btn-dark"
                                                      @click="$emit('increment', element.id)"
                                                  >
                                                      +
                                                  </button>
                                              </td>
                                              <td>
                                                  <button
                                                      class="btn btn-danger"
                                                      type="button"
                                                      @click="$emit('deleted', element.id)"
                                                  >
                                                      X
                                                  </button>
                                              </td>
                                          </tr>
                                      </tbody>
                                  </table>

                                  <p class="text-end">Total pagar: <span class="fw-bold">${{ total }}</span></p>
                                  <button class="btn btn-dark w-100 mt-3 p-2"
                                  @click="$emit('deleted-all', buy)"
                                  >Vaciar Carrito</button>
                                  </div>
                              </div>
                          </div>
                      </nav>
                  </div>
                  <div class="row mt-5">
                      <div class="col-md-6 text-center text-md-start pt-5">
                          <h1 class="display-2 fw-bold">Modelo {{ guitar.nombre }}</h1>
                          <p class="mt-5 fs-5 text-white">{{ guitar.descripcion }}</p>
                          <p class=" fs-1 fw-black">${{ guitar.precio }}</p>
                          <button 
                              type="button"
                              class="btn fs-4 bg-custom-me text-white py-2 px-5"
                              @click="$emit('add-buy',guitar)"
                          >Agregar al Carrito</button>
                      </div>
                  </div>
              </div>
                        <img class="header-guitarra" src="/img/header_guitarra.png" alt="imagen header">
          </header>
</template>
