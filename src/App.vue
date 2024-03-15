<template>
  <div>
    <!-- Encabezado con título y botón -->
    <header class="header"> 
      <h1>Lista de Asistencia</h1>
      <button class="button ver-registros-btn" @click="verRegistrosPorDia">Ver Registros por Día</button>
      <button class="button guardar-registros-btn" @click="guardarRegistros">Guardar Registros</button>
    </header>

    <!-- Formulario de registro de asistencia -->
    <div class="container">
      <div class="form-group">
        <label for="documento">Documento:</label>
        <input v-model="documento" type="text" id="documento" class="documento-input" />
      </div>
      <div class="form-group">
        <button class="button registrar-asistencia-btn" @click="registrarAsistencia">Registrar Asistencia</button>
      </div>
    </div>

    <!-- Mostrar registros -->
    <div class="container registros-container">
      <h2>Registros</h2>
      <div class="registro">
        <div class="registro-header">
          <span>Nombre</span>
          <span>Documento</span>
          <span>Fecha y Hora</span>
        </div>
        <div v-for="(registro, index) in registros" :key="index" class="registro-item">
          <span>{{ registro.nombre }}</span>
          <span>{{ registro.documento }}</span>
          <span>{{ registro.fechaHora }}</span>
        </div>
      </div>
    </div>

    <!-- Selección de fecha para ver registros por día -->
    <div v-if="mostrarFiltroFecha" class="container">
      <label for="fecha">Seleccionar Fecha:</label>
      <input type="date" v-model="fechaSeleccionada" id="fecha">
      <button class="button" @click="filtrarRegistrosPorDia">Filtrar</button> <!-- Nuevo botón de filtrar -->
    </div>

    <!-- Tabla para mostrar registros por día -->
    <div v-if="mostrarRegistrosPorDia" class="container">
      <h2>Registros por Día</h2>
      <template v-if="registrosFiltrados.length > 0">
        <table>
          <thead>
            <tr>
              <th>Nombre</th>
              <th>Documento</th>
              <th>Fecha y Hora</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="(registro, index) in registrosFiltrados" :key="index">
              <td>{{ registro.nombre }}</td>
              <td>{{ registro.documento }}</td>
              <td>{{ registro.fechaHora }}</td>
            </tr>
          </tbody>
        </table>
      </template>
      <template v-else>
        <p>No hay registros para la fecha seleccionada.</p>
      </template>
    </div>

    <!-- Pie de página -->
    <footer class="footer">
      <p>&copy; 2024 Mi Aplicación</p>
    </footer>
  </div>
</template>

<script>
import Swal from 'sweetalert2';
import moment from "moment";

export default {
  data() {
    return {
      documento: "",
      grupoClases: [
      { nombre: "Juan Perez", documento: "1234567890" },
  { nombre: "María García", documento: "0987654321" },
  { nombre: "Carlos López", documento: "1122334455" },
  { nombre: "Laura Rodríguez", documento: "6677889900" },
  { nombre: "Andrés Martínez", documento: "5544332211" },
  { nombre: "Sofía Gómez", documento: "9988776655" },
  { nombre: "Luisa González", documento: "3366998877" },
  { nombre: "Pedro Ramírez", documento: "7788990011" },
  { nombre: "Ana Sánchez", documento: "0011223344" },
  { nombre: "Alejandro Vargas", documento: "5566778899" },
  { nombre: "Valentina Herrera", documento: "9876543210" },
  { nombre: "Daniel Ospina", documento: "1029384756" },
  { nombre: "Camila Zapata", documento: "1212121212" },
  { nombre: "Mateo Bedoya", documento: "3434343434" },
  { nombre: "Lucía Valencia", documento: "5656565656" },
  { nombre: "Julián Quintero", documento: "7878787878" },
  { nombre: "Mariana Duque", documento: "9898989898" },
  { nombre: "Diego Mendoza", documento: "5555555555" },
  { nombre: "Catalina Ríos", documento: "7777777777" },
  { nombre: "Sebastián Giraldo", documento: "9999999999" }
      ],
      registros: [],
      registrosGuardados: [], // Variable para almacenar los registros guardados
      mostrarFiltroFecha: false,
      fechaSeleccionada: "",
    };
  },
  computed: {
    registrosFiltrados() {
      return this.registros.filter(registro =>
        moment(registro.fechaHora, "DD/MM/YYYY, HH:mm:ss").format("YYYY-MM-DD") === this.fechaSeleccionada
      );
    },
    mostrarRegistrosPorDia() {
      return this.mostrarFiltroFecha && this.registrosFiltrados.length > 0;
    }
  },
  methods: {
    buscarEstudiante(documento) {
      return this.registros.find(registro => registro.documento === documento);
    },
    validarRegistro() {
      if (!this.documento) {
        Swal.fire({
          icon: 'error',
          title: 'Campo Vacío',
          text: 'Por favor, complete el campo de documento.',
        });
        return false;
      }

      if (this.buscarEstudiante(this.documento)) {
        Swal.fire({
          icon: 'error',
          title: 'Documento Duplicado',
          text: 'El documento ya está registrado.',
        });
        return false;
      }

      return true;
    },
    registrarAsistencia() {
      if (!this.validarRegistro()) {
        return;
      }

      const estudiante = this.grupoClases.find(estudiante => estudiante.documento === this.documento);

      if (estudiante) {
        const fechaActual = moment();
        const fechaFormateada = fechaActual.format("DD/MM/YYYY, HH:mm:ss");
        const registroItem = {
          nombre: estudiante.nombre,
          documento: estudiante.documento,
          fechaHora: fechaFormateada,
        };

        this.registros.push(registroItem);
        this.documento = "";
        Swal.fire({
          icon: 'success',
          title: 'Registro Exitoso',
          text: 'El estudiante ha sido registrado correctamente.',
        });
      } else {
        Swal.fire({
          icon: 'error',
          title: 'Estudiante no encontrado',
          text: 'El documento no coincide con ningún estudiante en el grupo.',
        });
      }
    },
    guardarRegistros() {
      if (this.registros.length === 0) {
        Swal.fire({
          icon: 'warning',
          title: 'Sin Registros',
          text: 'No se ha registrado ningún estudiante hoy.',
        });
        return;
      }

      // Guardar los registros en registrosGuardados
      this.registrosGuardados = [...this.registros];

      // Limpiar registros
      this.registros = [];

      this.documento = "";

      Swal.fire({
        icon: 'success',
        title: 'Registros Guardados',
        text: 'Los registros se han guardado correctamente.',
      });

      // Habilitar la opción de ver registros por día después de guardar los registros
      this.mostrarFiltroFecha = true;
    },

    filtrarRegistrosPorDia() {
      this.registros = this.registrosGuardados.filter(registro =>
        moment(registro.fechaHora, "DD/MM/YYYY, HH:mm:ss").format("YYYY-MM-DD") === this.fechaSeleccionada
      );
      this.mostrarFiltroFecha = false; // Ocultar la selección de fecha después de filtrar
    },
    
    verRegistrosPorDia() {
      if (this.registros.length === 0) {
        Swal.fire({
          icon: 'warning',
          title: 'Sin Registros',
          text: 'No se ha registrado ningún estudiante hoy.',
        });
      } else {
        this.mostrarFiltroFecha = true;
      }
    },
  },
};
</script>

<style>
/* Estilos generales */
body {
  font-family: Arial, sans-serif;
  background-color: #f7f7f7;
  color: #333;
  margin: 0;
  padding: 0;
}

.container {
  max-width: 800px;
  margin: 50px auto;
  padding: 20px;
  background-color: #fff;
  border-radius: 10px;
  box-shadow: 0px 0px 10px rgba(0, 0, 0, 0.1);
}

.header {
  background-color: #333;
  color: white;
  padding: 20px;
  text-align: center;
  border-radius: 10px 10px 0 0;
}

.footer {
  background-color: #333;
  color: white;
  padding: 10px 20px;
  text-align: center;
  position: fixed;
  width: 100%;
  bottom: 0;
  left: 0;
}

.button {
  padding: 12px 24px;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
  margin-right: 10px;
}

.button:last-child {
  margin-right: 0;
}

.button:hover {
  background-color: #45a049;
}

/* Estilos específicos para botones */
.ver-registros-btn {
  background-color: #3498db;
}

.registrar-asistencia-btn {
  background-color: #f39c12;
}

.guardar-registros-btn {
  background-color: #2ecc71;
}

.form-group {
  margin-bottom: 20px;
}

.label-documento {
  display: block;
  margin-bottom: 5px;
}

.documento-input {
  width: calc(100% - 20px);
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.registrar-asistencia-btn {
  display: block;
  width: calc(100% - 20px);
  margin-top: 10px;
}

.registros-container {
  margin-top: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  padding: 10px;
  overflow: auto;
}

.registro {
  display: flex;
  flex-direction: column;
}

.registro-header {
  display: flex;
  justify-content: space-between;
  background-color: #4caf50;
  color: white;
  padding: 10px;
  border-bottom: 1px solid #ccc;
}

.registro-item {
  display: flex;
  justify-content: space-between;
  padding: 10px;
  border-bottom: 1px solid #ccc;
}

.registro-item:last-child {
  border-bottom: none;
}

/* Estilos para la tabla de registros por día */
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

th, td {
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
}

th {
  background-color: #4caf50;
  color: white;
}
</style>
