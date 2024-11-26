<template>
    <div class="clientes-container">
      
      <form @submit.prevent="agregarServicio" class="formulario">
        <h2>Agregar Servicio</h2>
        <div>
          <label>Tipo de Servicio:</label>
          <input v-model="nuevoServicio.tipo_servicio" type="text" required class="input-field" />
        </div>
        <div>
          <label>Fecha:</label>
          <input v-model="nuevoServicio.fecha" type="date" required class="input-field" />
        </div>
        <div>
          <label>Hora:</label>
          <input v-model="nuevoServicio.hora" type="time" required class="input-field" />
        </div>
        <div>
          <label>Costo:</label>
          <input v-model="nuevoServicio.costo" type="number" required class="input-field" />
        </div>
        <div>
          <label>ID Vehículo:</label>
          <input v-model="nuevoServicio.vehiculo_id" type="number" required class="input-field" />
        </div>
        <button type="submit" class="btn-agregar">Agregar</button>
      </form>
  
   <div class="tabla-clientes">
      <table border="1">
        <thead>
          <tr>
            <th>ID</th>
            <th>Tipo</th>
            <th>Fecha</th>
            <th>Hora</th>
            <th>Costo</th>
            <th>ID Vehículo</th>
            <th>Acciones</th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="servicio in servicios" :key="servicio.id">
            <td>{{ servicio.id }}</td>
            <td>
              <input
                v-if="servicioEditable && servicioEditable.id === servicio.id"
                v-model="servicioEditable.tipo_servicio"
                type="text"
                class="input-edit"
              />
              <span v-else>{{ servicio.tipo_servicio }}</span>
            </td>
            <td>
              <input
                v-if="servicioEditable && servicioEditable.id === servicio.id"
                v-model="servicioEditable.fecha"
                type="date"
                class="input-edit"
              />
              <span v-else>{{ servicio.fecha }}</span>
            </td>
            <td>
              <input
                v-if="servicioEditable && servicioEditable.id === servicio.id"
                v-model="servicioEditable.costo"
                type="number"
                class="input-edit"
              />
              <span v-else>{{ servicio.costo }}</span>
            </td>
            <td>
              <input
                v-if="servicioEditable && servicioEditable.id === servicio.id"
                v-model="servicioEditable.vehiculo_id"
                type="number"
                class="input-edit"
              />
              <span v-else>{{ servicio.vehiculo_id }}</span>
            </td>
            <td>
              <button
                v-if="!servicioEditable || servicioEditable.id !== servicio.id"
                @click="editarServicio(servicio)"
                class="btn-editar"
              >
                Editar
              </button>
              <button v-else @click="guardarCambios" class="btn-guardar">
                Guardar
              </button>
              <button @click="eliminarServicio(servicio.id)" class="btn-eliminar">
                Eliminar
              </button>
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
  name: 'ServiciosTable',
  setup() {
    const servicios = ref([]);
    const nuevoServicio = ref({
      tipo_servicio: '',
      fecha: '',
      hora: '',
      costo: '',
      vehiculo_id: '',
    });

    const servicioEditable = ref(null); 

   
    const obtenerServicios = async () => {
      try {
        const response = await fetch('http://localhost:3000/servicios');
        servicios.value = await response.json();
      } catch (error) {
        console.error('Error obteniendo servicios:', error);
      }
    };
 
 const agregarServicio = async () => {
      try {
        const response = await fetch('http://localhost:3000/servicios', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(nuevoServicio.value),
        });

        if (response.ok) {
          alert('Servicio agregado exitosamente');
          obtenerServicios();
          nuevoServicio.value = {
            tipo_servicio: '',
            fecha: '',
            hora: '',
            costo: '',
            vehiculo_id: '',
          };
        } else {
          alert('Error al agregar servicio');
        }
      } catch (error) {
        console.error('Error al agregar servicio:', error);
      }
    };

 const editarServicio = (servicio) => {
      servicioEditable.value = { ...servicio }; 
    };

   
    const guardarCambios = async () => {
      if (!servicioEditable.value || !servicioEditable.value.id) {
        alert('Seleccione un servicio para editar');
        return;
      }

      try {
        const response = await fetch(` http://localhost:3000/servicios/${servicioEditable.value.id}`, {
          method: 'PUT',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify(servicioEditable.value), 
        });

        if (response.ok) {
          alert('Servicio actualizado exitosamente');
          servicioEditable.value = null; 
          obtenerServicios(); 
        } else {
          alert('Error al actualizar servicio');
        }
      } catch (error) {
        console.error('Error al guardar cambios:', error);
      }
    };
  
  const eliminarServicio = async (id) => {
      if (confirm('¿Estás seguro de eliminar este servicio?')) {
        try {
          const response = await fetch(` http://localhost:3000/servicios/${id}`, {
            method: 'DELETE',
          });

          if (response.ok) {
            alert('Servicio eliminado exitosamente');
            obtenerServicios();
          } else {
            alert('Error al eliminar servicio');
          }
        } catch (error) {
          console.error('Error al eliminar servicio:', error);
        }
      }
    };

    
    onMounted(obtenerServicios);

    return {
      servicios,
      nuevoServicio,
      servicioEditable,
      agregarServicio,
      editarServicio,
      guardarCambios,
      eliminarServicio,
    };
  },
};
</script>

<style scoped>
/* Contenedor principal */
.servicios-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 40px;
  padding: 40px;
  background: linear-gradient(135deg, #4facfe, #00f2fe);
  min-height: 100vh;
  font-family: 'Roboto', sans-serif;
  animation: fadeIn 2s ease;
}

/* Título */
h2 {
  font-size: 3rem;
  font-weight: bold;
  color: #ffffff;
  margin-bottom: 30px;
  text-align: center;
  text-transform: uppercase;
  letter-spacing: 3px;
  text-shadow: 4px 4px 10px rgba(0, 0, 0, 0.3);
  animation: bounceIn 1.5s ease;
}

/* Estilo del formulario */
.formulario {
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  gap: 20px;
  width: 100%;
  max-width: 900px;
  padding: 30px;
  background: rgba(255, 255, 255, 0.9);
  border-radius: 15px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
  transform: translateY(0);
  transition: transform 0.4s ease, box-shadow 0.4s ease;
}

.formulario:hover {
  transform: translateY(-5px);
  box-shadow: 0 15px 35px rgba(0, 0, 0, 0.3);
}

/* Inputs */
.formulario input[type="text"],
.formulario input[type="number"],
.formulario select {
  width: calc(50% - 10px);
  padding: 15px;
  border: none;
  border-radius: 10px;
  font-size: 1.1rem;
  box-sizing: border-box;
  background: #f0f8ff;
  box-shadow: inset 0 4px 8px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.formulario input[type="text"]:focus,
.formulario input[type="number"]:focus,
.formulario select:focus {
  transform: scale(1.03);
  box-shadow: 0 0 10px rgba(79, 172, 254, 0.5);
  outline: none;
}

/* Botón */
.formulario button {
  padding: 15px 30px;
  font-size: 1.2rem;
  background: linear-gradient(90deg, #4facfe, #00f2fe);
  color: white;
  border: none;
  border-radius: 10px;
  cursor: pointer;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

.formulario button:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 15px rgba(79, 172, 254, 0.4);
}

/* Tabla */
.tabla-servicios {
  width: 100%;
  max-width: 1000px;
  background: rgba(255, 255, 255, 0.95);
  padding: 30px;
  border-radius: 15px;
  box-shadow: 0 10px 25px rgba(0, 0, 0, 0.2);
  animation: slideIn 1.5s ease;
}

table {
  width: 100%;
  border-collapse: collapse;
  text-align: center;
  font-size: 1.1rem;
}

th, td {
  padding: 20px;
  border: 1px solid #ddd;
}

th {
  background: linear-gradient(90deg, #4facfe, #00f2fe);
  color: white;
  text-transform: uppercase;
  font-weight: bold;
}

td {
  background-color: #f7f7f7;
  transition: background-color 0.3s ease, transform 0.3s ease;
}

td:hover {
  background-color: #e0f7fa;
  transform: scale(1.02);
}

/* Botón dentro de la tabla */
button {
  padding: 12px 20px;
  font-size: 1rem;
  background: linear-gradient(90deg, #4facfe, #00f2fe);
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: transform 0.3s ease, box-shadow 0.3s ease;
}

button:hover {
  transform: translateY(-3px);
  box-shadow: 0 8px 15px rgba(79, 172, 254, 0.4);
}

button:disabled {
  background: #ccc;
  cursor: not-allowed;
}

/* Animaciones */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

@keyframes bounceIn {
  0% {
    transform: scale(0.8);
    opacity: 0.5;
  }
  50% {
    transform: scale(1.1);
    opacity: 0.8;
  }
  100% {
    transform: scale(1);
    opacity: 1;
  }
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateX(-30px);
  }
  to {
    opacity: 1;
    transform: translateX(0);
  }
}

/* Responsividad */
@media (max-width: 768px) {
  .formulario {
    flex-direction: column;
    gap: 20px;
  }

  .formulario input[type="text"],
  .formulario input[type="number"],
  .formulario select {
    width: 100%;
  }

  h2 {
    font-size: 2rem;
  }
}
</style>
