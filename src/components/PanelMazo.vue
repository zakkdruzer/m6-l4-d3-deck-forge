<script setup>
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

const handleAjustar = (idCarta, nuevaCantidad) => {
  emit('ajustar', idCarta, nuevaCantidad);
};

const handleQuitar = (idCarta) => {
  emit('quitar', idCarta);
};
</script>

<template>
  <section class="panel-mazo">
    <h2>Mi mazo</h2>

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