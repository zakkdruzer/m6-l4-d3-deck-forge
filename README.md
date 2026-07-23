# Deck Forge · Constructor de mazos en Vue

Aplicación web construida con Vue 3 + Vite que permite explorar un catálogo de cartas, armar mazos con reglas, y visualizar estadísticas como curva de maná, coste promedio y distribución por tipo. Proyecto integrador del módulo 6 (Lección 4) basado en la misión **Deck Forge**.

## Descripción general

Deck Forge es un constructor de mazos de cartas pensado para practicar:

- Componentes y arquitectura abuelo → padre → hijo.
- Estado reactivo con `ref` y `computed`.
- Comunicación por props y eventos (`defineProps`, `defineEmits`).
- Directivas de renderizado (`v-for`, `v-if`, `v-show`, `:class`, `v-model`).
- Persistencia de datos en `localStorage`.
- Build y despliegue con Vite.

La aplicación se divide en dos columnas principales:

- **Catálogo**: buscador y filtros para explorar cartas.
- **Mi mazo**: panel donde se construye el mazo y se muestran estadísticas.

## Funcionalidades

### Nivel 1 · Catálogo y mazo

- Muestra el catálogo completo: una carta por ítem con nombre, emoji, tipo, coste y rareza.
- Borde y color dinámico de la carta según rareza usando clases condicionadas.
- Buscador por nombre en vivo: la lista se filtra a medida que se escribe.
- Agregar cartas al mazo desde el catálogo; el mazo se renderiza en la columna derecha.
- El estado del mazo vive en `App.vue` y se pasa por props a los componentes hijos.

### Nivel 2 · Reglas y gestión

- Filtros por tipo, coste y rareza con valores derivados.
- Mensaje “Ninguna carta coincide” cuando no hay resultados en el catálogo.
- En el mazo se puede subir y bajar la cantidad de cada carta y quitar cartas.
- Reglas del mazo:
  - Máximo 30 cartas totales.
  - Máximo 2 copias de la misma carta.
- Avisos visuales cuando se alcanzan los límites (30 cartas y 2 copias).
- Estadísticas básicas del mazo:
  - Cantidad total de cartas.
  - Coste promedio calculado a partir de las cartas y sus cantidades.

### Nivel 3 · Para lucirse

- Curva de maná:
  - Una barra horizontal por cada coste (0…10).
  - Ancho proporcional a la cantidad de cartas de ese coste.
- Distribución por tipo:
  - Conteo de cartas por tipo (Criatura, Hechizo, Trampa, etc.).
  - Porcentaje respecto del total de cartas del mazo.
  - Barras horizontales para visualizar la proporción de cada tipo.
- Orden del catálogo:
  - Ordenar por nombre (A–Z).
  - Ordenar por coste (menor a mayor).
- Contador de resultados visibles:
  - Muestra cuántas cartas se están viendo después de aplicar filtros y búsqueda.
- Persistencia:
  - El mazo sobrevive al recargar gracias a `localStorage`.
  - Extra: guardar el mazo con un nombre y volver a cargarlo desde una lista de mazos guardados.

## Arquitectura de componentes

La app sigue una arquitectura de tres niveles: abuelo → padre → hijo.

- `App.vue` (abuelo)
  - Dueño del estado del catálogo y del mazo.
  - Gestiona reglas de negocio, estadísticas base y persistencia.
  - Conecta el catálogo con el panel de mazo.
- `Catalogo.vue` (padre A)
  - Recibe el catálogo por props.
  - Maneja buscador, filtros y orden.
  - Emite `agregar` hacia `App.vue` cuando se añade una carta al mazo.
  - Renderiza cada carta mediante `CartaCatalogo.vue`.
- `CartaCatalogo.vue` (hijo A)
  - Renderiza una carta del catálogo.
  - Muestra nombre, emoji, tipo, coste y rareza.
  - Envía eventos de agregar carta al padre.
- `PanelMazo.vue` (padre B)
  - Recibe el mazo y el catálogo por props.
  - Renderiza la lista de cartas del mazo con `CartaMazo.vue`.
  - Emite eventos `ajustar` y `quitar` hacia `App.vue`.
  - Integra el componente `StatsMazo.vue` para mostrar estadísticas.
- `CartaMazo.vue` (hijo B)
  - Representa una carta dentro del mazo con controles de cantidad.
  - Botones para subir/bajar cantidad y quitar carta.
  - Muestra avisos visuales al alcanzar el límite de copias.
- `StatsMazo.vue` (hijo B)
  - Recibe `mazo` y `catalogo` por props.
  - Calcula cantidad total de cartas y coste promedio.
  - Construye la curva de maná (conteo por coste).
  - Calcula distribución de cartas por tipo en porcentaje y la representa como barras.

## Estado y reactividad

- `ref`:
  - `catalogo`: lista de cartas base.
  - `mazo`: lista de entradas `{ id, cantidad }`.
  - `nombreMazo`: nombre del mazo actual para guardar.
  - `mazosGuardados`: lista de mazos guardados con nombre.
  - Estados de UI en `Catalogo.vue`: texto de búsqueda, filtros y orden.
- `computed`:
  - `totalCartas`: suma de cantidades en el mazo.
  - `costeMedio`: coste medio ponderado por cantidad.
  - `cartasFiltradas`: catálogo filtrado por búsqueda, tipo, rareza, coste y orden.
  - `curvaMana`: conteo de cartas por coste.
  - `distribucionTipos`: conteo y porcentaje por tipo.

## Persistencia

La app utiliza `localStorage` para:

- Guardar el mazo actual cada vez que cambia.
- Restaurar el mazo al iniciar la aplicación.
- Guardar la lista de mazos guardados por nombre.
- Restaurar la lista de mazos guardados al iniciar.

## Tecnologías

- Vue 3 (`<script setup>`)
- Vite
- JavaScript (sin TypeScript)
- CSS global (`style.css`) para layout y estilos compartidos.

## Ejecución y build

- Desarrollo: `npm run dev`
- Build de producción: `npm run build`
- El build generado se utiliza para desplegar la app en producción.

## Conceptos del módulo aplicados

- Componentes `.vue` separados para cada responsabilidad.
- `ref` y `computed` para manejar estado reactivo y valores derivados.
- `v-for` con `:key` para listas de cartas y mazos.
- `v-if` y `v-show` para estados vacíos y mensajes condicionales.
- `:class` dinámico para bordes según rareza y avisos de límites.
- `v-model` en inputs y selects para buscador y filtros.
- `watch` y `localStorage` para persistencia.
- `defineProps` y `defineEmits` para comunicación entre componentes.
- Arquitectura abuelo → padre → hijo con datos que bajan por props y eventos que suben.

## Puedes ver el resultado en:

https://zakkdruzer.github.io/m6-l4-d3-deck-forge/
