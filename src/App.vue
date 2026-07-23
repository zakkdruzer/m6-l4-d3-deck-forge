<script setup>
import { ref, computed, watch } from 'vue';

// Catálogo base (ejemplo)
const catalogo = ref([
  { id: 1, nombre: 'Goblin', emoji: '☘', tipo: 'Criatura', coste: 1, rareza: 'Común' },
  { id: 2, nombre: 'Dragón de fuego', emoji: '🐉', tipo: 'Criatura', coste: 8, rareza: 'Épica' },
  { id: 3, nombre: 'Bola de fuego', emoji: '🔥', tipo: 'Hechizo', coste: 4, rareza: 'Rara' },
  // agrega más cartas para probar
]);

// Estado del mazo: solo guarda id + cantidad
const mazo = ref([]); // [{ id: 1, cantidad: 2 }, ...]

const totalCartas = computed(() =>
  mazo.value.reduce((acc, entrada) => acc + entrada.cantidad, 0)
);

const costeMedio = computed(() => {
  if (totalCartas.value === 0) return 0;
  const costeTotal = mazo.value.reduce((acc, entrada) => {
    const carta = catalogo.value.find(c => c.id === entrada.id);
    return carta ? acc + carta.coste * entrada.cantidad : acc;
  }, 0);
  return Number((costeTotal / totalCartas.value).toFixed(2));
});

// Función para agregar carta al mazo respetando reglas (máx 30, máx 2 copias)
const agregarCartaAlMazo = (idCarta) => {
  const entradaExistente = mazo.value.find((entrada) => entrada.id === idCarta);

  const totalActual = mazo.value.reduce((acc, e) => acc + e.cantidad, 0);
  if (totalActual >= 30) {
    // Aquí luego puedes poner un aviso visual
    return;
  }

  if (!entradaExistente) {
    mazo.value.push({ id: idCarta, cantidad: 1 });
  } else if (entradaExistente.cantidad < 2) {
    entradaExistente.cantidad += 1;
  } else {
    // Aquí también puedes avisar: ya tiene 2 copias
  }
};

const ajustarCantidadCarta = (idCarta, nuevaCantidad) => {
  const entrada = mazo.value.find((e) => e.id === idCarta);
  if (!entrada) return;

  // Si nuevaCantidad es 0, la quitamos
  if (nuevaCantidad <= 0) {
    mazo.value = mazo.value.filter((e) => e.id !== idCarta);
    return;
  }

  // Respetar límite de 2 copias
  if (nuevaCantidad > 2) {
    entrada.cantidad = 2;
  } else {
    entrada.cantidad = nuevaCantidad;
  }

  // Respetar límite de 30 totales
  const totalActual = mazo.value.reduce((acc, e) => acc + e.cantidad, 0);
  if (totalActual > 30) {
    // Si te pasas, puedes ajustar o mostrar aviso; por simplicidad, no dejamos pasar
    entrada.cantidad -= (totalActual - 30);
  }
};

const quitarCartaDelMazo = (idCarta) => {
  mazo.value = mazo.value.filter((e) => e.id !== idCarta);
};

// Persistencia básica: guardar cada vez que cambie el mazo
watch(
  mazo,
  (nuevo) => {
    localStorage.setItem('mazo', JSON.stringify(nuevo));
  },
  { deep: true }
);

// Cargar mazo al iniciar (si existe)
const guardado = localStorage.getItem('mazo');
if (guardado) {
  mazo.value = JSON.parse(guardado);
}
</script>

<template>
  <main class="app">
    <h1>Deck Forge</h1>

    <section class="layout">
      <Catalogo
        :catalogo="catalogo"
        @agregar="agregarCartaAlMazo"
      />

      <!-- Más adelante aquí irá PanelMazo -->
      <section>
        <p>Total de cartas en el mazo: {{ totalCartas }}</p>
        <p>Coste medio: {{ costeMedio }}</p>
      </section>
    </section>
  </main>
</template>

<style scoped>
.app {
  padding: 2rem;
}
</style>