<script setup>
import { defineProps, defineEmits } from 'vue';

const props = defineProps({
  carta: {
    type: Object,
    required: true,
  },
});

const emit = defineEmits(['agregar']);

const handleAgregar = () => {
  emit('agregar', props.carta.id);
};
</script>

<template>
  <article class="carta" :class="['carta--' + carta.rareza.toLowerCase()]">
    <span class="carta__emoji">{{ carta.emoji }}</span>
    <h3 class="carta__nombre">{{ carta.nombre }}</h3>
    <p class="carta__tipo">{{ carta.tipo }}</p>
    <p class="carta__coste">Coste: {{ carta.coste }}</p>
    <button type="button" @click="handleAgregar">+</button>
  </article>
</template>