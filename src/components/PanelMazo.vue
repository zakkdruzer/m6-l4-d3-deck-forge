<script setup>
import { computed } from 'vue';
import { defineProps, defineEmits } from 'vue';
import CartaMazo from './CartaMazo.vue';
import StatsMazo from './StatsMazo.vue';

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

const obtenerCarta = (id) => props.catalogo.find((c) => c.id === id);

const totalCartas = computed(() =>
  props.mazo.reduce((acc, entrada) => acc + entrada.cantidad, 0)
);

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

    <p v-if="totalCartas >= 30" class="panel-mazo__alerta">
      Has alcanzado el máximo de 30 cartas en el mazo.
    </p>

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

    <StatsMazo
      :catalogo="catalogo"
      :mazo="mazo"
    />
  </section>
</template>