<script setup>
  import { ref, onMounted, watch } from "vue";
  import { db } from "./data/guitarras";
  import Guitarra from "./components/Guitarra.vue";
  import Header from "./components/Header.vue";
  import Footer from "./components/Footer.vue";

  const guitarras = ref([]);
  const carrito = ref([]);
  const guitarra = ref({});
  
  watch(carrito, ()=>{
    guardarLocalStorage();
  },{
    deep:true
  });

  onMounted(()=>{
    guitarras.value = db;
    guitarra.value = db[3];

    carrito.value = JSON.parse(localStorage.getItem('carrito')) ?? [];
  });

  const guardarLocalStorage = () => {
    localStorage.setItem('carrito', JSON.stringify(carrito.value));
  }

  const agregarCarrito = (guitarra) => {
    const existeEnCarrito = carrito.value.findIndex(producto => producto.id === guitarra.id);
    if(existeEnCarrito>=0){
        carrito.value[existeEnCarrito].cantidad++;
    }else{
        guitarra.cantidad = 1;
        carrito.value.push(guitarra);
    }
  }

  const decrementarCantidad = (id) => {
    const indexProducto = carrito.value.findIndex(producto => producto.id === id);
    if(indexProducto>=0){
        if(carrito.value[indexProducto].cantidad>1){
            carrito.value[indexProducto].cantidad--;
        }
    }
  } 

  const incrementarCantidad = (id) => {
    const indexProducto = carrito.value.findIndex(producto => producto.id === id);
    if(indexProducto>=0){
        if(carrito.value[indexProducto].cantidad<5){
            carrito.value[indexProducto].cantidad++;
        }
    }
  }

  const eliminarProducto = (id) => {
    carrito.value = carrito.value.filter(productoState => productoState.id !== id);
  }

  const vaciarCarrito = () => {
    carrito.value = [];
  }
</script>

<template>
    <Header
        :guitarra="guitarra"
        :carrito="carrito"
        @agregar-carrito="agregarCarrito"
        @eliminar-producto="eliminarProducto"
        @incrementar-cantidad="incrementarCantidad"
        @decrementar-cantidad="decrementarCantidad"
        @vaciar-carrito="vaciarCarrito"
    />
    <main class="container-xl mt-5">
        <h2 class="text-center">Nuestra Colección</h2>

        <div class="row mt-5">
            <Guitarra
                v-for="guitarra in guitarras"
                :guitarra="guitarra"
                @agregar-carrito="agregarCarrito"
            />
        </div>
    </main>
    <Footer />
</template>
