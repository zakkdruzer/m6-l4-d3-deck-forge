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

// Curva de maná (ya la tienes)
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

const maxCantidad = computed(() => {
  return Math.max(...curvaMana.value.map((item) => item.cantidad), 1);
});

// NUEVO: distribución por tipo en porcentaje
const distribucionTipos = computed(() => {
  const conteos = {};

  props.mazo.forEach((entrada) => {
    const carta = props.catalogo.find((c) => c.id === entrada.id);
    if (!carta) return;
    const tipo = carta.tipo;
    if (!conteos[tipo]) {
      conteos[tipo] = 0;
    }
    conteos[tipo] += entrada.cantidad;
  });

  const tipos = Object.keys(conteos);

  return tipos.map((tipo) => {
    const cantidad = conteos[tipo];
    const porcentaje =
      totalCartas.value === 0
        ? 0
        : Number(((cantidad / totalCartas.value) * 100).toFixed(1));

    return { tipo, cantidad, porcentaje };
  });
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
            :style="{ width: (item.cantidad / maxCantidad) * 100 + '%' }"
          ></div>
        </div>
        <span class="curva-mana__cantidad">{{ item.cantidad }}</span>
      </div>
    </div>

    <h4>Distribución por tipo</h4>
    <div class="distribucion-tipos">
      <div
        v-for="item in distribucionTipos"
        :key="item.tipo"
        class="distribucion-tipos__fila"
      >
        <span class="distribucion-tipos__tipo">{{ item.tipo }}</span>
        <div class="distribucion-tipos__barra-wrap">
          <div
            class="distribucion-tipos__barra"
            :style="{ width: item.porcentaje + '%' }"
          ></div>
        </div>
        <span class="distribucion-tipos__porcentaje">
          {{ item.porcentaje }}%
        </span>
      </div>
    </div>
  </section>
</template>