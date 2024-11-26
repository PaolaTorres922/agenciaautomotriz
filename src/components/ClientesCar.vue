<template>
    <div class="clientes-container">
      
      <div class="formulario">
        <h2>Agregar Cliente</h2>
        <form @submit.prevent="agregarCliente">
          <div>
            <label>Nombre:</label>
            <input v-model="nuevoCliente.nombre" type="text" required />
          </div>
          <div>
            <label>Apellido:</label>
            <input v-model="nuevoCliente.apellido" type="text" required />
          </div>
          <div>
            <label>Dirección:</label>
            <input v-model="nuevoCliente.direccion" type="text" required />
          </div>
          <div>
            <label>Teléfono:</label>
            <input v-model="nuevoCliente.telefono" type="text" required />
          </div>
          <div>
            <label>Email:</label>
            <input v-model="nuevoCliente.email" type="email" required />
          </div>
          <button type="submit">Agregar Cliente</button>
        </form>
      </div>
  
     
      <div class="tabla-clientes">
        <h2>Lista de Clientes</h2>
        <table>
          <thead>
            <tr>
              <th>ID</th>
              <th>Nombre</th>
              <th>Apellido</th>
              <th>Dirección</th>
              <th>Teléfono</th>
              <th>Email</th>
              <th>Acciones</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="cliente in clientes" :key="cliente.id">
              <td>{{ cliente.id }}</td>
              <td>
                <input
                  v-if="clienteEditable.id === cliente.id"
                  v-model="clienteEditable.nombre"
                  type="text"
                />
                <span v-else>{{ cliente.nombre }}</span>
              </td>
              <td>
                <input
                  v-if="clienteEditable.id === cliente.id"
                  v-model="clienteEditable.apellido"
                  type="text"
                />
                <span v-else>{{ cliente.apellido }}</span>
              </td>
              <td>
                <input
                  v-if="clienteEditable.id === cliente.id"
                  v-model="clienteEditable.direccion"
                  type="text"
                />
                <span v-else>{{ cliente.direccion }}</span>
              </td>
              <td>
                <input
                  v-if="clienteEditable.id === cliente.id"
                  v-model="clienteEditable.telefono"
                  type="text"
                />
                <span v-else>{{ cliente.telefono }}</span>
              </td>
              <td>
                <input
                  v-if="clienteEditable.id === cliente.id"
                  v-model="clienteEditable.email"
                  type="text"
                />
                <span v-else>{{ cliente.email }}</span>
              </td>
              <td>
                <button v-if="clienteEditable.id !== cliente.id" @click="editarCliente(cliente)">
                  Editar
                </button>
                <button v-else @click="guardarCambios">
                  Guardar
                </button>
                <button v-if="clienteEditable.id === cliente.id" @click="cancelarEdicion">
                  Cancelar
                </button>
                <button @click="eliminarCliente(cliente.id)">Eliminar</button>
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
    name: 'ClientesTable',
    setup() {
      const clientes = ref([]);
      const nuevoCliente = ref({
        nombre: '',
        apellido: '',
        direccion: '',
        telefono: '',
        email: '',
      });
      const clienteEditable = ref({ id: null, nombre: '', apellido: '', direccion: '', telefono: '', email: '' });
  
      const obtenerClientes = async () => {
        try {
          const response = await fetch('http://localhost:3000/clientes');
          clientes.value = await response.json();
        } catch (error) {
          console.error('Error obteniendo clientes:', error);
        }
      };
  
      const agregarCliente = async () => {
        try {
          const response = await fetch('http://localhost:3000/clientes', {
            method: 'POST',
            headers: {
              'Content-Type': 'application/json',
            },
            body: JSON.stringify(nuevoCliente.value),
          });
  
          if (response.ok) {
            alert('Cliente agregado exitosamente');
            obtenerClientes();
            Object.keys(nuevoCliente.value).forEach((key) => (nuevoCliente.value[key] = '')); 
          } else {
            alert('Error al agregar cliente');
          }
        } catch (error) {
          console.error('Error al agregar cliente:', error);
        }
      };
  
      const editarCliente = (cliente) => {
        clienteEditable.value = { ...cliente };
      };
  
      const cancelarEdicion = () => {
        clienteEditable.value = { id: null, nombre: '', apellido: '', direccion: '', telefono: '', email: '' };
      };
  
      const guardarCambios = async () => {
        try {
          const response = await fetch(`http://localhost:3000/clientes/${clienteEditable.value.id}`, {
            method: 'PUT',
            headers: {
              'Content-Type': 'application/json',
            },
            body: JSON.stringify(clienteEditable.value),
          });
  
          if (response.ok) {
            alert('Cliente actualizado exitosamente');
            cancelarEdicion();
            obtenerClientes();
          } else {
            alert('Error al guardar cambios');
          }
        } catch (error) {
          console.error('Error al guardar cambios:', error);
        }
      };
  
      const eliminarCliente = async (id) => {
        if (confirm('¿Estás seguro de eliminar este cliente?')) {
          try {
            const response = await fetch(`http://localhost:3000/clientes/${id}`, {
              method: 'DELETE',
            });
  
            if (response.ok) {
              alert('Cliente eliminado exitosamente');
              obtenerClientes();
            } else {
              alert('Error al eliminar cliente');
            }
          } catch (error) {
            console.error('Error al eliminar cliente:', error);
          }
        }
      };
  
      onMounted(obtenerClientes);
  
      return {
        clientes,
        nuevoCliente,
        clienteEditable,
        agregarCliente,
        editarCliente,
        guardarCambios,
        cancelarEdicion,
        eliminarCliente,
      };
    },
  };
  </script>

<style scoped>

.clientes-container {
  display: flex;
  justify-content: space-between;
  gap: 30px;
  padding: 30px;
  background: linear-gradient(135deg, #fce4ec, #f8bbd0);
  border-radius: 15px;
  box-shadow: 0 8px 24px rgba(0, 0, 0, 0.1);
  flex-wrap: wrap;
  animation: slideIn 0.8s ease-out;
}


@keyframes slideIn {
  0% {
    transform: translateX(-100%);
  }
  100% {
    transform: translateX(0);
  }
}


.formulario {
  width: 45%;
  padding: 25px;
  background-color: #ffffff;
  border: 2px solid #ff4081;
  border-radius: 10px;
  box-shadow: 0 4px 15px rgba(255, 64, 129, 0.1);
  transition: transform 0.3s ease, box-shadow 0.3s ease-in-out;
  animation: fadeIn 0.6s ease-in-out;
}

.formulario:hover {
  transform: scale(1.05);
  box-shadow: 0 6px 25px rgba(255, 64, 129, 0.3);
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


.tabla-clientes {
  width: 50%;
  padding: 20px;
  background-color: #ffffff;
  border: 2px solid #ff4081;
  border-radius: 10px;
  box-shadow: 0 4px 15px rgba(255, 64, 129, 0.1);
  overflow-x: auto;
  max-height: 450px;
  animation: fadeInTable 1s ease-in-out;
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


h2 {
  font-size: 2rem;
  color: #ff4081;
  margin-bottom: 20px;
  text-align: center;
  font-family: 'Roboto', sans-serif;
}

form {
  display: grid;
  gap: 20px;
}

label {
  font-size: 1.2rem;
  font-weight: 600;
  color: #ff4081;
}


input[type="text"],
input[type="email"],
input[type="number"] {
  padding: 12px;
  border: 2px solid #ff4081;
  border-radius: 8px;
  font-size: 1.1rem;
  width: 100%;
  box-sizing: border-box;
  transition: border 0.3s ease;
}

input[type="text"]:focus,
input[type="email"]:focus,
input[type="number"]:focus {
  border-color: #e91e63;
  outline: none;
}


button {
  padding: 12px 24px;
  font-size: 1.1rem;
  background-color: #ff4081;
  color: white;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background-color 0.3s ease, transform 0.3s ease;
  width: 100%;
}

button:hover {
  background-color: #e91e63;
  transform: translateY(-2px);
}

button:disabled {
  background-color: #ddd;
  cursor: not-allowed;
}


button:last-child {
  background-color: #dc3545;
}

button:last-child:hover {
  background-color: #c82333;
}

/* Estilo de la tabla */
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
  background-color: #fafafa;
}

th,
td {
  padding: 12px 15px;
  text-align: left;
}

th {
  background-color: #ff4081;
  color: white;
  font-weight: bold;
}

td {
  background-color: #f9f9f9;
  transition: background-color 0.3s ease;
}

td:hover {
  background-color: #f1f1f1;
}

tr:nth-child(even) td {
  background-color: #f1f1f1;
}


@media (max-width: 768px) {
  .clientes-container {
    flex-direction: column;
    gap: 20px;
  }

  .formulario,
  .tabla-clientes {
    width: 100%;
  }
}
</style>

