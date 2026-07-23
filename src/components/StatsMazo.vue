<script setup>
import { computed } from 'vue';
import { defineProps } from 'vue';

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

// Total de cartas en el mazo
const totalCartas = computed(() =>
  props.mazo.reduce((acc, entrada) => acc + entrada.cantidad, 0)
);

// Coste promedio
const costeMedio = computed(() => {
  if (totalCartas.value === 0) return 0;
  const costeTotal = props.mazo.reduce((acc, entrada) => {
    const carta = props.catalogo.find((c) => c.id === entrada.id);
    return carta ? acc + carta.coste * entrada.cantidad : acc;
  }, 0);
  return Number((costeTotal / totalCartas.value).toFixed(2));
});

// Conteo por coste (0 a 10)
const curvaMana = computed(() => {
  const conteos = Array.from({ length: 11 }, (_, coste) => ({
    coste,
    cantidad: 0,
  }));

  props.mazo.forEach((entrada) => {
    const carta = props.catalogo.find((c) => c.id === entrada.id);
    if (!carta) return;
    const coste = carta.coste;
    if (coste >= 0 && coste <= 10) {
      conteos[coste].cantidad += entrada.cantidad;
    }
  });

  return conteos;
});

// Máximo para escalar barras (evita barras enormes)
const maxCantidad = computed(() => {
  return Math.max(...curvaMana.value.map((item) => item.cantidad), 1);
});
</script>

<template>
  <section class="stats-mazo">
    <h3>Estadísticas del mazo</h3>

    <p>Cantidad total de cartas: {{ totalCartas }}</p>
    <p>Coste promedio: {{ costeMedio }}</p>

    <h4>Curva de maná</h4>
    <div class="curva-mana">
      <div
        v-for="item in curvaMana"
        :key="item.coste"
        class="curva-mana__fila"
      >
        <span class="curva-mana__coste">{{ item.coste }}</span>
        <div class="curva-mana__barra-wrap">
          <div
            class="curva-mana__barra"
            :style="{
              width: (item.cantidad / maxCantidad) * 100 + '%',
            }"
          ></div>
        </div>
        <span class="curva-mana__cantidad">{{ item.cantidad }}</span>
      </div>
    </div>
  </section>
</template>