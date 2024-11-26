<template>
    <div class="clientes-container">
   
      <div class="formulario">
        <h2>Agregar Vehículo</h2>
        <form @submit.prevent="agregarVehiculo">
          <div>
            <label>Marca:</label>
            <input v-model="nuevoVehiculo.marca" type="text" required />
          </div>
          <div>
            <label>Modelo:</label>
            <input v-model="nuevoVehiculo.modelo" type="text" required />
          </div>
          <div>
            <label>Año:</label>
            <input v-model="nuevoVehiculo.anio" type="number" required class="campo-numero" />
          </div>
          <div>
            <label>Color:</label>
            <input v-model="nuevoVehiculo.color" type="text" required />
          </div>
          <div>
            <label>Placa:</label>
            <input v-model="nuevoVehiculo.placa" type="text" required />
          </div>
          <div>
            <label>ID Cliente:</label>
          <input v-model="nuevoVehiculo.cliente_id" type="number" required class="campo-numero" />
        </div>
        <button type="submit">Agregar</button>
      </form>
    </div>

  
    <div class="tabla-clientes">
      <h2>Lista de Vehículos</h2>
      <table border="1">
        <thead>
          <tr>
            <th>ID</th>
            <th>Marca</th>
            <th>Modelo</th>
            <th>Año</th>
            <th>Color</th>
            <th>Placa</th>
            <th>ID Cliente</th>
            <th>Acciones</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="vehiculo in vehiculos" :key="vehiculo.id">
            <td>{{ vehiculo.id }}</td>
            <td>
                <input
                v-if="vehiculoEditable.id === vehiculo.id"
                v-model="vehiculoEditable.marca"
                type="text"
              />
              <span v-else>{{ vehiculo.marca }}</span>
            </td>
            <td>
              <input
                v-if="vehiculoEditable.id === vehiculo.id"
                v-model="vehiculoEditable.modelo"
                type="text"
              />
              <span v-else>{{ vehiculo.modelo }}</span>
            </td>
            <td>
              <input
                v-if="vehiculoEditable.id === vehiculo.id"
                v-model="vehiculoEditable.anio"
                type="number"
                class="campo-numero"
              />
              <span v-else>{{ vehiculo.anio }}</span>
            </td>
            <td>
              <input
                v-if="vehiculoEditable.id === vehiculo.id"
                v-model="vehiculoEditable.color"
                type="text"
              />
              <span v-else>{{ vehiculo.color }}</span>
            </td>
            <td>
                <input
                v-if="vehiculoEditable.id === vehiculo.id"
                v-model="vehiculoEditable.placa"
                type="text"
              />
              <span v-else>{{ vehiculo.placa }}</span>
            </td>
            <td>
              <input
                v-if="vehiculoEditable.id === vehiculo.id"
                v-model="vehiculoEditable.cliente_id"
                type="number"
                class="campo-numero"
              />
              <span v-else>{{ vehiculo.cliente_id }}</span>
            </td>
            <td>
              <button v-if="vehiculoEditable.id !== vehiculo.id" @click="editarVehiculo(vehiculo)">
                Editar
              </button>
              <button v-else @click="guardarCambios">
                Guardar
              </button>
              <button @click="eliminarVehiculo(vehiculo.id)">Eliminar</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script>
import { ref, onMounted } from 'vue';

export default {
  name: 'VehiculosTable',
  setup() {
    const vehiculos = ref([]);
    const nuevoVehiculo = ref({
      marca: '',
      modelo: '',
      anio: '',
      color: '',
      placa: '',
      cliente_id: '',
    });

    const vehiculoEditable = ref({
      id: null,
      marca: '',
      modelo: '',
      anio: '',
      color: '',
      placa: '',
      cliente_id: '',
    });

    const obtenerVehiculos = async () => {
      try {
        const response = await fetch('http://localhost:3000/vehiculos');
        vehiculos.value = await response.json();
      } catch (error) {
        console.error('Error obteniendo vehículos:', error);
      }
    };
    const agregarVehiculo = async () => {
      try {
        const response = await fetch('http://localhost:3000/vehiculos', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(nuevoVehiculo.value),
        });

        if (response.ok) {
          alert('Vehículo agregado exitosamente');
          obtenerVehiculos();
          nuevoVehiculo.value = {
            marca: '',
            modelo: '',
            anio: '',
            color: '',
            placa: '',
            cliente_id: '',
          };
        } else {
          alert('Error al agregar vehículo');
        }
      } catch (error) {
        console.error('Error al agregar vehículo:', error);
      }
    };

    const editarVehiculo = (vehiculo) => {
      vehiculoEditable.value = { ...vehiculo }; 
    };
    const guardarCambios = async () => {
      try {
        const response = await fetch(` http://localhost:3000/vehiculos/${vehiculoEditable.value.id}`, {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(vehiculoEditable.value),
        });

        if (response.ok) {
          alert('Vehículo actualizado exitosamente');
          vehiculoEditable.value = { id: null, marca: '', modelo: '', anio: '', color: '', placa: '', cliente_id: '' };
          obtenerVehiculos();
        } else {
          alert('Error al actualizar vehículo');
        }
      } catch (error) {
        console.error('Error al guardar cambios:', error);
      }
    };

    const eliminarVehiculo = async (id) => {
      if (confirm('¿Estás seguro de eliminar este vehículo?')) {
        try {
          const response = await fetch(` http://localhost:3000/vehiculos/${id}`, {
            method: 'DELETE',
          });
          if (response.ok) {
            alert('Vehículo eliminado exitosamente');
            obtenerVehiculos();
          } else {
            alert('Error al eliminar vehículo');
          }
        } catch (error) {
          console.error('Error al eliminar vehículo:', error);
        }
      }
    };

    onMounted(obtenerVehiculos);

    return {
      vehiculos,
      nuevoVehiculo,
      vehiculoEditable,
      agregarVehiculo,
      editarVehiculo,
      guardarCambios,
      eliminarVehiculo,
    };
  },
};
</script>

<style scoped>

.vehiculos-container {
  display: flex;
  flex-direction: column;
  gap: 30px;
  padding: 30px;
  background: linear-gradient(135deg, #e3f2fd, #ffffff); 
  min-height: 100vh;
  animation: fadeIn 1s ease-out;
}


@keyframes fadeIn {
  0% {
    opacity: 0;
    transform: translateY(20px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}


h2 {
  font-size: 2.5rem;
  font-weight: bold;
  color: #007bff;
  margin-bottom: 20px;
  text-align: center;
  text-transform: uppercase;
  letter-spacing: 2px;
  animation: slideInFromLeft 1s ease-out;
}


@keyframes slideInFromLeft {
  0% {
    opacity: 0;
    transform: translateX(-50px);
  }
  100% {
    opacity: 1;
    transform: translateX(0);
  }
}


.formulario {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  width: 100%;
  max-width: 900px;
  padding: 20px;
  background-color: #ffffff;
  border-radius: 12px;
  box-shadow: 0 8px 20px rgba(0, 123, 255, 0.2);
  animation: fadeIn 1.2s ease-in-out;
  transition: transform 0.3s ease-in-out;
}

.formulario:hover {
  transform: scale(1.03);
}


.formulario input[type="text"],
.formulario input[type="number"],
.formulario select {
  width: 100%;
  padding: 12px;
  border: 2px solid #007bff;
  border-radius: 8px;
  font-size: 1.1rem;
  box-sizing: border-box;
  transition: border-color 0.3s ease;
}

.formulario input[type="text"]:focus,
.formulario input[type="number"]:focus,
.formulario select:focus {
  border-color: #0056b3;
}


.formulario button {
  padding: 12px 20px;
  font-size: 1.1rem;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  width: 100%;
  max-width: 220px;
}

.formulario button:hover {
  background-color: #0056b3;
  transform: translateY(-3px);
}

.formulario button:active {
  transform: translateY(1px);
}


.tabla-vehiculos {
  width: 100%;
  padding: 20px;
  background-color: #ffffff;
  border-radius: 12px;
  box-shadow: 0 8px 20px rgba(0, 123, 255, 0.1);
  overflow-x: auto;
  margin-top: 30px;
  animation: fadeInTable 1.5s ease-in-out;
}


@keyframes fadeInTable {
  0% {
    opacity: 0;
    transform: translateY(20px);
  }
  100% {
    opacity: 1;
    transform: translateY(0);
  }
}


table {
  width: 100%;
  border-collapse: collapse;
  text-align: left;
}


th, td {
  padding: 15px;
  text-align: left;
  border-bottom: 1px solid #ddd;
  transition: background-color 0.3s ease;
}

th {
  background-color: #007bff;
  color: white;
  font-weight: bold;
}

td {
  background-color: #f9f9f9;
}

td:hover {
  background-color: #f1f1f1;
}

tr:nth-child(even) td {
  background-color: #f1f1f1;
}


button {
  padding: 8px 16px;
  font-size: 1rem;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.3s ease, transform 0.3s ease;
}

button:hover {
  background-color: #0056b3;
  transform: translateY(-3px);
}

button:active {
  transform: translateY(1px);
}

button:disabled {
  background-color: #ddd;
  cursor: not-allowed;
}


@media (max-width: 768px) {
  .vehiculos-container {
    padding: 20px;
  }

  .formulario {
    flex-direction: column;
    gap: 15px;
  }

  .tabla-vehiculos {
    padding: 15px;
  }

  table {
    font-size: 1rem;
  }

  th, td {
    padding: 12px;
  }

  button {
    width: 100%;
  }
}
</style>

