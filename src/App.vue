<script setup>
import { ref, reactive, onMounted } from "vue";
import { db } from "./data/guitarras";
import Guitarra from "./components/Guitarra.vue";
import Header from "./components/Header.vue";
import Footer from "./components/Footer.vue";

const guitarras = ref([]);
const carrito = ref([]);
const guitarraPrincipal = ref({});

onMounted(() => {
  guitarras.value = db;
  guitarraPrincipal.value = db[3];
});

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
  // Validar que no sea una cantidad menor a cero
  if (carrito.value[guitarra].cantidad <= 1) return;
  // Restar cantidad
  carrito.value[guitarra].cantidad--;
};

const sumarProductos = (id) => {
  const guitarra = carrito.value.findIndex((producto) => producto.id === id);
  // Validar que no sea una cantidad mayor a cinco
  if (carrito.value[guitarra].cantidad >= 5) return;
  //sumar cantidad
  carrito.value[guitarra].cantidad++;
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
