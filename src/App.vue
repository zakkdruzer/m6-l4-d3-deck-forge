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

// Ejemplo de persistencia simple (luego la puedes mejorar)
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
    <!-- Aquí irán más adelante <Catalogo /> y <PanelMazo /> -->
    <p>Total de cartas en el mazo: {{ totalCartas }}</p>
    <p>Coste medio: {{ costeMedio }}</p>
  </main>
</template>

<style scoped>
.app {
  padding: 2rem;
}
</style>