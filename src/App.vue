<template>
  <div id="app">
    <h1>Agencia Automotriz</h1>
    <nav>
      <button @click="view = 'ClientesCar'">Clientes</button>
      <button @click="view = 'VehiculosCar'">Vehículos</button>
      <button @click="view = 'ServiciosCar'">Servicios</button>
      <button @click="view = 'OfertasCar'">Ofertas</button> 
    </nav>

   
    <div>
      <ClientesCar v-if="view === 'ClientesCar'" />
      <VehiculosCar v-if="view === 'VehiculosCar'" />
      <ServiciosCar v-if="view === 'ServiciosCar'" />
      <OfertasCar v-if="view === 'OfertasCar'" />
    </div>
  </div>
</template>

<script>
import ClientesCar from './components/ClientesCar.vue';
import VehiculosCar from './components/VehiculosCar.vue';
import ServiciosCar from './components/ServiciosCar.vue';
import OfertasCar from './components/OfertasCar.vue';

export default {
  name: 'App',
  components: {
    ClientesCar,
    VehiculosCar,
    ServiciosCar,
    OfertasCar,
  },
  data() {
    return {
      view: 'ClientesCar', 
    };
  },
};
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  text-align: center;
}
nav {
  margin-bottom: 20px;
}
button {
  margin: 5px;
  padding: 10px 15px;
  cursor: pointer;
  border: none;
  background-color: #007bff;
  color: white;
  border-radius: 5px;
}
button:hover {
  background-color: #0056b3;
}
button:focus {
  outline: none;
}
</style>