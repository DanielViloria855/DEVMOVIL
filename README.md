# 🐛 Reporte de Bugs y Features (FoodieApp)

A continuación se encuentra el mapeo detallado de las **8 Features (Fixures)** solicitadas en el README y los **Bugs Intencionales** identificados dentro del código fuente del proyecto.

---

## ✨ Features (Características por Implementar)

| # | Feature | Especificación | Ubicación (Archivo) | Línea Aprox. |
|---|---------|----------------|---------------------|--------------|
| **F01** | **Barra de Búsqueda en Menú** | `TextInput` encima de la lista de platos para filtrar por nombre de forma dinámica. | `src/screens/MenuScreen.js` | ~96 |
| **F02** | **Sistema de Favoritos** | Botón (♡/♥) para guardar/eliminar platos en `AsyncStorage`. | `src/screens/DishDetailScreen.js` | ~37-43 |
| **F03** | **Notas Especiales en el Carrito** | `TextInput` por cada item del carrito para agregar especificaciones ("sin cebolla", etc). | `src/screens/CartScreen.js` | ~50-68 (en `renderCartItem`) |
| **F04** | **Filtro Horizontal por Categoría** | ScrollView horizontal con "chips" (Entradas, Sopas, etc) para filtrar la lista. | `src/screens/MenuScreen.js` | ~96 |
| **F05** | **Toggle de Modo Oscuro** | Componente `Switch` persistido en storage para alternar el tema visual en el Perfil. | `src/screens/ProfileScreen.js` | ~80 |
| **F06** | **Cálculo de Total con IVA (19%)** | Mostrar en el footer del carrito Subtotal, IVA 19% y Total final calculado dinámicamente. | `src/screens/CartScreen.js` | ~110-123 |
| **F07** | **Sistema de Calificación Post-Pedido**| Sistema de 5 estrellas interactivas en tarjetas de pedido con estado `"delivered"`. | `src/screens/OrderHistoryScreen.js` | ~102-106 |
| **F08** | **Selector de Cantidad (+/−)** | Botones `+` y `-` para modificar cantidades de platos directamente en el carrito. | `src/screens/CartScreen.js` | ~53-66 |

---

## 🐞 Bugs Intencionales (Errores en el código)

### 1. Loop Infinito en Menú (Rendimiento)
*   **Especificación**: El hook `useEffect` que hace fetch del menú no tiene un arreglo de dependencias vacío `[]`, lo que causará que llame a Firebase/Data en cada ciclo de renderizado de la app.
*   **Ubicación**: `src/screens/MenuScreen.js`
*   **Línea**: 14-16

### 2. Evento `onClick` en lugar de `onPress`
*   **Especificación**: El componente `TouchableOpacity` que agrega productos al carrito usa la prop `onClick` de la web, cuando en React Native debe ser `onPress`.
*   **Ubicación**: `src/screens/DishDetailScreen.js`
*   **Línea**: 63

### 3. Falta Importación de `<ScrollView>` (ReferenceError)
*   **Especificación**: Se está utilizando el componente `ScrollView` como contenedor pero no ha sido importado desde 
    la librería de `react-native`. Causará crash al intentar abrir las pantallas.
*   **Ubicación**: `src/screens/DishDetailScreen.js` (línea 30) y `src/screens/ProfileScreen.js` (línea 77)
*   **Línea de importación**: 2

### 4. Lectura/Escritura de JSON mal manejado (`AsyncStorage`)
*   **Especificación**: `AsyncStorage` solo maneja cadenas (strings). Al guardar el carrito o el perfil de usuario se le pasa directamente un objeto/arreglo en lugar de serializarlo con `JSON.stringify()`. Al cargarlo no se está usando `JSON.parse()`.
*   **Ubicación 1**: `src/context/CartContext.js` (Líneas 24 y 33)
*   **Ubicación 2**: `src/screens/ProfileScreen.js` (Línea 24)

### 5. `cartCount` no se actualiza (Reactividad Rota)
*   **Especificación**: El `useEffect` 
que recalcula el número de artículos (`cartCount`) tiene un array de dependencias vacío 
`[]`. Por lo tanto, el total del header/tab bar jamás se actualizará después del primer render. Debería ser `[cartItems]`.
*   **Ubicación**: `src/context/CartContext.js`
*   **Línea**: 18

### 6. Mutación de Estado Directa en el Contexto
*   **Especificación**: Para eliminar del carrito se hace `const updatedCart = cartItems; updatedCart.splice(...)`. Esto muta la variable original en lugar de clonarla con el operador spread (`[...cartItems]`), arruinando el ciclo de detección de cambios de React.
*   **Ubicación**: `src/context/CartContext.js`
*   **Líneas**: 53-56

### 7. Duplicidad de items en el carrito (`addToCart`)
*   **Especificación**: Al agregar al carrito, siempre hace `push` del item creando uno nuevo con `quantity: 1`. No verifica si el `id` ya existe en el carrito para simplemente sumarle a la cantidad actual.
*   **Ubicación**: `src/context/CartContext.js`
*   **Líneas**: 40-47

### 8. Renderizado ineficiente (`keyExtractor`)
*   **Especificación**: Se está usando `index.toString()` en lugar del ID único del elemento `item.id`. Esto es considerado una mala práctica de performance y estabilidad al usar el `FlatList`.
*   **Ubicación**: `src/screens/MenuScreen.js`
*   **Línea**: 99
