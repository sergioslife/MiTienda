<template>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.3.0/css/all.min.css">

  
  <div class="form-container">
  <!-- Esta es una sección que contiene un formulario para agregar artículos a la tienda -->
  
  <h1>Tienda</h1>
  <!-- Título de la sección, en este caso, "Tienda" -->
  
  <form @submit.prevent="agregarArticulo">
    <!-- Un formulario que ejecuta la función "agregarArticulo" cuando se envía -->
    
    <div>
      <label for="nombre">Artículo:</label>
      <!-- Etiqueta de texto para identificar el campo de entrada -->
      
      <br>
      <!-- Salto de línea para separar la etiqueta del campo de entrada -->
      
      <input v-model="nombre" type="text" id="nombre" placeholder="Ingrese el nombre del artículo" required>
      <!-- Campo de entrada de texto para ingresar el nombre del artículo -->
      <!-- El atributo "v-model" está vinculado a la propiedad "nombre" de Vue.js -->
      <!-- El atributo "type" especifica que es un campo de texto -->
      <!-- El atributo "id" es un identificador único para el campo -->
      <!-- El atributo "placeholder" muestra un texto de ejemplo en el campo -->
      <!-- El atributo "required" hace que el campo sea obligatorio -->
    </div>
    
    <div>
      <label for="cantidad">Cantidad:</label>
      <br>
      <input v-model.number="cantidad" type="number" id="cantidad" placeholder="Ingrese la cantidad" required>
      <!-- Campo de entrada numérica para ingresar la cantidad de artículos -->
      <!-- Similar a la explicación anterior, pero con tipo "number" -->
    </div>
    
    <div>
      <label for="valor">Valor Unitario:</label>
      <br>
      <input v-model.number="valor" type="number" id="valor" placeholder="Ingrese el valor unitario en pesos $" required>
      <!-- Campo de entrada numérica para ingresar el valor unitario -->
    </div>
    
    <br>
    <!-- Salto de línea para separar los campos del botón de envío -->
    
    <button type="submit">Agregar</button>
    <!-- Botón de envío del formulario, que agrega el artículo al carrito -->
  </form>
</div>

    <!-- Tabla para mostrar los artículos agregados -->
    <div class="table-container">
  <!-- Un contenedor para la tabla que muestra los artículos en el carrito -->

  <table>
    <!-- Inicio de la tabla -->

    <!-- Encabezado de la tabla -->
    <thead>
      <!-- Sección del encabezado de la tabla -->
      <tr>
        <!-- Fila de encabezado de la tabla -->
        <th>#</th>
        <!-- Columna del número de artículo -->
        <th>Artículo</th>
        <!-- Columna del nombre del artículo -->
        <th>Cantidad</th>
        <!-- Columna de la cantidad de artículos -->
        <th>Valor unitario</th>
        <!-- Columna del valor unitario de los artículos -->
        <th>Valor total</th>
        <!-- Columna del valor total por artículo -->
        <th></th>
        <!-- Columna vacía para los botones de eliminar artículo -->
      </tr>
    </thead>

    <!-- Cuerpo de la tabla -->
    <tbody>
      <!-- Sección del cuerpo de la tabla -->
      <!-- Iteración sobre los artículos en el carrito -->
      <tr v-for="(articulo, index) in carrito" :key="index">
        <!-- Fila de la tabla generada dinámicamente para cada artículo en el carrito -->
        <td>{{ index + 1 }}</td>
        <!-- Columna que muestra el número de artículo (índice + 1) -->
        <td>{{ articulo.nombre }}</td>
        <!-- Columna que muestra el nombre del artículo desde los datos del carrito -->
        <td>{{ articulo.cantidad }}</td>
        <!-- Columna que muestra la cantidad de artículos desde los datos del carrito -->
        <td>${{ articulo.valor }}</td>
        <!-- Columna que muestra el valor unitario del artículo desde los datos del carrito -->
        <td>${{ articulo.valorTotal }}</td>
        <!-- Columna que muestra el valor total por artículo (cantidad * valor unitario) desde los datos del carrito -->
        <td>
          <!-- Columna para los botones de eliminar artículo -->
          <button @click="eliminarArticulo(index)">
            <!-- Botón que llama a la función "eliminarArticulo" cuando se hace clic -->
            <i class="fas fa-trash-alt"></i>
            <!-- Icono de basura para representar la acción de eliminar -->
          </button>
        </td>
      </tr>
    </tbody>
  </table>
  <!-- Fin de la tabla -->

  <!-- Resumen de la compra -->
  <div class="resumen" style="float: right;">
    <!-- Un contenedor para mostrar el resumen de la compra -->
    <p>Total Compra: ${{ totalCompra }}</p>
    <!-- Párrafo que muestra el total de la compra -->
    <p>Descuento ({{ descuentoPorcentaje }}%): ${{ descuento }}</p>
    <!-- Párrafo que muestra el descuento aplicado -->
    <p>Total a Pagar: ${{ totalConDescuento }}</p>
    <!-- Párrafo que muestra el total a pagar después del descuento -->
  </div>
</div>

</template>

<script>
export default {
  data() {
    return {
      nombre: '',        // Nombre del artículo (inicializado como cadena vacía)
      cantidad: 0,       // Cantidad de artículos (inicializada a 0)
      valor: 0,          // Valor del artículo (inicializado a 0)
      carrito: [],       // Array para almacenar los artículos agregados al carrito
    };
  },
  computed: {
    // Calcular el total de la compra
    totalCompra() {
      // Utiliza el método "reduce" para sumar los valores totales de todos los artículos en el carrito
      return this.carrito.reduce((total, articulo) => total + articulo.valorTotal, 0);
    },
    // Calcular el descuento aplicable
    descuento() {
      let descuentoPorCantidad = 0; // Inicializa el descuento por cantidad en 0
      let descuentoPorPrecio = 0;   // Inicializa el descuento por precio en 0

      // Condición 1: Si el total de la compra es mayor o igual a $60000, aplica un descuento del 5%
      if (this.totalCompra >= 60000) {
        descuentoPorPrecio = 0.05;
      }

      // Condición 2: Si el total de la compra es mayor o igual a $120000, aplica un descuento del 10%
      // Si se cumple esta condición, reemplaza el descuento anterior del 5%
      if (this.totalCompra >= 120000) {
        descuentoPorPrecio = 0.1;
      }

      // Condición 3: Si el total de la compra es mayor o igual a $240000, aplica un descuento del 15%
      // Si se cumple esta condición, reemplaza el descuento anterior del 10%
      if (this.totalCompra >= 240000) {
        descuentoPorPrecio = 0.15;
      }

      // Condición 4: Si la cantidad de artículos en el carrito es mayor o igual a 6, aplica un descuento del 10%
      if (this.carrito.length >= 6) {
        descuentoPorCantidad = 0.1;
      }

      // Condición 5: Si la cantidad de artículos en el carrito es mayor o igual a 12, aplica un descuento del 20%
      // Si se cumple esta condición, reemplaza el descuento anterior del 10%
      if (this.carrito.length >= 12) {
        descuentoPorCantidad = 0.2;
      }

      // Selecciona el mayor descuento entre descuentoPorCantidad y descuentoPorPrecio
      const mayorDescuento = Math.max(descuentoPorCantidad, descuentoPorPrecio);

      // Aplica el descuento al total de la compra y lo devuelve
      return mayorDescuento * this.totalCompra;
    },
 // Calcular el porcentaje de descuento real
 descuentoPorcentaje() {
      // Esta función calcula el porcentaje de descuento real aplicado a la compra
      
      // Calcula el porcentaje dividiendo el descuento entre el total de la compra y multiplicando por 100
      const porcentaje = (this.descuento / this.totalCompra) * 100;
      
      // Limita el resultado a 2 decimales utilizando el método "toFixed"
      return porcentaje.toFixed(2); // Devuelve el porcentaje formateado con 2 decimales
    },
    // Calcular el total a pagar con el descuento aplicado
    totalConDescuento() {
      // Esta función calcula el total a pagar después de aplicar el descuento
      
      // Resta el descuento del total de la compra y devuelve el resultado
      return this.totalCompra - this.descuento;
    },
  },
  methods: {
    // Agregar un artículo al carrito
    agregarArticulo() {
      // Esta función se llama cuando se agrega un artículo al carrito
      
      // Agrega un objeto con los detalles del artículo al array "carrito"
      this.carrito.push({
        nombre: this.nombre,
        cantidad: this.cantidad,
        valor: this.valor,
        valorTotal: this.cantidad * this.valor, // Calcula el valor total del artículo
      });

      // Limpia los campos del formulario para futuras entradas
      this.nombre = '';
      this.cantidad = 0;
      this.valor = 0;
    },
    // Eliminar un artículo del carrito
    eliminarArticulo(index) {
      // Esta función se llama cuando se elimina un artículo del carrito
      
      // Utiliza el método "splice" para eliminar el artículo en la posición especificada por "index"
      this.carrito.splice(index, 1);
    },
  },
};
</script>

<style scoped>
  /* Las reglas de estilo CSS que definen el aspecto y diseño de los elementos en tu componente Vue.js */
  
  form {
    background-color: azure;
    /* Establece el color de fondo del formulario en "azure" */
  }

  .form-container {
    width: 40%; /* Ocupa el 40% del ancho de la pantalla */
    padding: 10px;
    border: 2px solid #000;
    border-radius: 10px;
    /* Establece el ancho, el relleno, el borde y el radio de borde para el contenedor del formulario */
  }

  .table-container {
    border-radius: 10px;
    float: left; /* Alinea a la izquierda */
    /* Establece el radio de borde y alinea el contenedor de la tabla a la izquierda */
  }

  table {
    border-collapse: collapse;
    width: 100%;
    /* Establece el colapso de bordes en la tabla y ocupa el 100% del ancho disponible */
  }

  table, th, td {
    border: 2px solid #000;
    /* Establece un borde sólido de 2 píxeles en la tabla, las celdas de encabezado (th) y las celdas de datos (td) */
  }

  th, td {
    padding: 8px;
    text-align: left;
    /* Establece el relleno interior de las celdas y la alineación del texto a la izquierda */
  }

  .resumen {
    margin-top: 20px;
    float: right; /* Alinea a la derecha */
    /* Establece un margen superior y alinea el elemento con la clase "resumen" a la derecha */
  }
</style>
