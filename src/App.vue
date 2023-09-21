<script setup>
import { ref, reactive, onMounted, watch } from "vue";
import { db } from "./data/guitarras";
import Guitarra from "./components/Guitarra.vue";
import Header from "./components/Header.vue";
import Footer from "./components/Footer.vue";

const guitarras = ref([]);
const carrito = ref([]);
const guitarraPrincipal = ref({});

// Configuración de un observador (watcher) para el objeto 'carrito'
watch(
  carrito, // El objeto que se observará
  () => {
    guardarLocalStorage(); // Llama a la función guardarLocalStorage cuando el objeto 'carrito' cambie
  },
  {
    deep: true, // Observar cambios profundos en el objeto 'carrito', incluyendo sus propiedades internas
  }
);

onMounted(() => {
  guitarras.value = db;
  guitarraPrincipal.value = db[3];

  const carritoStorage = localStorage.getItem("carrito");
  if (carritoStorage) {
    // Si existe un valor en el Local Storage, lo convierte de nuevo en un objeto y lo asigna a 'carrito'
    carrito.value = JSON.parse(carritoStorage);
  }
});

const guardarLocalStorage = () => {
  // Convierte el objeto 'carrito' a una cadena JSON y lo almacena en el Local Storage bajo la clave "carrito"
  localStorage.setItem("carrito", JSON.stringify(carrito.value));
};

const agregarCarrito = (guitarra) => {
  // findIndex nos retorna la posición en la que se encuentra una coincidencia, si no encuentra nada retorna -1
  const existeGuitarra = carrito.value.findIndex(
    (producto) => producto.id === guitarra.id
  );

  if (existeGuitarra >= 0) {
    carrito.value[existeGuitarra].cantidad++;
  } else {
    guitarra.cantidad = 1;
    carrito.value.push(guitarra);
  }
};

const restarProductos = (id) => {
  const guitarra = carrito.value.findIndex((producto) => producto.id === id);
  // Valida que no sea una cantidad menor a cero
  if (carrito.value[guitarra].cantidad <= 1) return;
  // Resta la cantidad
  carrito.value[guitarra].cantidad--;
};

const sumarProductos = (id) => {
  const guitarra = carrito.value.findIndex((producto) => producto.id === id);
  // Valida que no sea una cantidad mayor a cinco
  if (carrito.value[guitarra].cantidad >= 5) return;
  //suma la cantidad
  carrito.value[guitarra].cantidad++;
};

const eliminarProducto = (id) => {
  carrito.value = carrito.value.filter((producto) => producto.id !== id);
};

const vaciarCarrito = () => {
  carrito.value = [];
};
</script>

<template>
  <div>
    <Header
      :carrito="carrito"
      :guitarraPrincipal="guitarraPrincipal"
      @restar-productos="restarProductos"
      @sumar-productos="sumarProductos"
      @agregar-carrito="agregarCarrito"
      @eliminar-producto="eliminarProducto"
      @vaciar-carrito="vaciarCarrito"
    />
    <main class="container-xl mt-5">
      <h2 class="text-center">Nuestra Colección</h2>

      <div class="row mt-5">
        <Guitarra
          v-for="guitarra in guitarras"
          :guitarra="guitarra"
          :key="guitarra.id"
          @agregar-carrito="agregarCarrito"
        />
      </div>
    </main>
    <Footer />
  </div>
</template>

<style scoped></style>
