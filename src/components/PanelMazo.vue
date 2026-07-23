<script setup>
import { computed } from 'vue';
import { defineProps, defineEmits } from 'vue';
import CartaMazo from './CartaMazo.vue';

const props = defineProps({
  catalogo: {
    type: Array,
    required: true,
  },
  mazo: {
    type: Array,
    required: true,
  },
});

const emit = defineEmits(['ajustar', 'quitar']);

// Derivados básicos
const totalCartas = computed(() =>
  props.mazo.reduce((acc, entrada) => acc + entrada.cantidad, 0)
);

const costeMedio = computed(() => {
  if (totalCartas.value === 0) return 0;
  const costeTotal = props.mazo.reduce((acc, entrada) => {
    const carta = props.catalogo.find((c) => c.id === entrada.id);
    return carta ? acc + carta.coste * entrada.cantidad : acc;
  }, 0);
  return Number((costeTotal / totalCartas.value).toFixed(2));
});

// Helper para encontrar datos de carta
const obtenerCarta = (id) => props.catalogo.find((c) => c.id === id);

const handleAjustar = (idCarta, nuevaCantidad) => {
  emit('ajustar', idCarta, nuevaCantidad);
};

const handleQuitar = (idCarta) => {
  emit('quitar', idCarta);
};
</script>

<template>
  <section class="panel-mazo">
    <h2>Mi mazo ({{ totalCartas }} / 30)</h2>

    <div class="panel-mazo__lista">
      <p v-if="props.mazo.length === 0">
        Aún no hay cartas en el mazo.
      </p>

      <CartaMazo
        v-for="entrada in props.mazo"
        :key="entrada.id"
        :entrada="entrada"
        :carta="obtenerCarta(entrada.id)"
        @ajustar="handleAjustar"
        @quitar="handleQuitar"
      />
    </div>

    <div class="panel-mazo__stats">
      <h3>Estadísticas</h3>
      <p>Cantidad total de cartas: {{ totalCartas }}</p>
      <p>Coste promedio del mazo: {{ costeMedio }}</p>
      <!-- Aquí luego añadimos curva de maná y distribución por tipo -->
    </div>
  </section>
</template>