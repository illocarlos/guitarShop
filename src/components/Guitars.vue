        <script setup>

//definimos la props de guitarra ya que viene de el componente padre y la sintasis es asi 

        const props = defineProps({
            guitar: {
            type: Object,
            required:true
        }
                })
//y usamos un metodo de otro componente por lo tanto se tiene que definir como defineEmits

//tanto defineProps y defineEmits son palabras reservadas pero no se importan 
      defineEmits(['add-buy','increment-buy'])
        </script>

        
<template>
    
             <div class="col-md-6 col-lg-4 my-4 row align-items-center ">
                    <div class="col-4 ">
                        <img class="img-fluid" :src="'/img/'+ guitar.imagen+'.jpg'"  :alt="guitar.nombre">
                    </div>
                    <div class="col-8 ">
                        <h3 class="text-black fs-4 fw-bold text-uppercase">{{ guitar.nombre }}</h3>
                        <p>{{ guitar.descripcion }}</p>
                        <p class="fw-black  fs-3">${{ guitar.precio }}</p>
                        <!-- se invoca los evento arriba en el script y abajo con : -->
                         <!-- @click="$emit('nombredefineemit',props que usarmeos en ese  metodo)" -->
                        <button 
                            type="button"
                            class="btn btn-dark w-100"
                            @click="$emit('add-buy',guitar)"
                        >Agregar al Carrito</button>
                    </div>
                </div>
 
</template>