# Carrito de Compras - Vue 3

Mi primera aplicación de carrito de compras hecha con Vue 3 para practicar y aprender el framework.

## ¿Qué hace?

Una tienda online simple donde puedes:

- Ver productos de la Fake Store API
- Agregar productos al carrito
- Cambiar cantidades (+ y -)
- Ver el total de tu compra
- El carrito se guarda aunque cierres el navegador

## Cómo usarlo

1. Clonar el repo:

```bash
git clone [tu-repo]
cd vue-shopping-cart
```

2. Instalar dependencias:

```bash
npm install
```

3. Ejecutar:

```bash
npm run dev
```

4. Abrir http://localhost:5173

## Lo que aprendí haciendo esto

- **Vue 3 Composition API** - Usar `ref()` y `computed()`
- **Lifecycle hooks** - `onMounted()` para cargar datos
- **API calls** - Fetch con async/await
- **localStorage** - Guardar el carrito en el navegador
- **Props y Events** - Comunicación entre componentes
- **Watchers** - Para guardar automáticamente el carrito

## Tecnologías usadas

- Vue 3
- Vite
- Tailwind CSS
- Fake Store API

## Problemas que resolví

### Error de precisión decimal

Al principio el total salía como `103.96000000000001` en lugar de `103.96`. Lo arreglé usando:

```javascript
return Math.round(total * 100) / 100;
```

### Error en localStorage

A veces se rompía al cargar datos del carrito. Agregué un try/catch:

```javascript
try {
  const parsedCart = JSON.parse(cartStorage);
  cart.value = parsedCart;
} catch (error) {
  localStorage.removeItem("cart"); // Limpiar datos rotos
}
```

### Advertencia de eventos

Vue me decía que no había declarado los eventos. Solucioné con:

```javascript
const emit = defineEmits(["decrement-quantity", "increment-quantity"]);
```

## Archivos principales

- `App.vue` - Componente principal con la lógica del carrito
- `CardProducts.vue` - Tarjeta de cada producto
- `Header.vue` - Header con el carrito

## Cosas que me gustaría mejorar

- Hacer que se vea mejor en mobile
- Agregar un filtro de búsqueda
- Animaciones cuando agregas al carrito
- Tal vez un checkout básico

## Notas

Este es mi proyecto de práctica para aprender Vue 3. Probablemente hay formas mejores de hacer algunas cosas, pero funcionó bien para entender los conceptos básicos.

La API que uso es gratuita: https://fakestoreapi.com/products

---

Si tienes sugerencias o encuentras bugs, ¡me ayudarías mucho! 😊
