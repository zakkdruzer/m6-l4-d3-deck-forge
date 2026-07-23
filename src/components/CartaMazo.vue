<script setup>
import { defineProps, defineEmits } from 'vue';

const props = defineProps({
  entrada: {
    type: Object,
    required: true, // { id, cantidad }
  },
  carta: {
    type: Object,
    required: true,
  },
});

const emit = defineEmits(['ajustar', 'quitar']);

const subir = () => {
  emit('ajustar', props.entrada.id, props.entrada.cantidad + 1);
};

const bajar = () => {
  emit('ajustar', props.entrada.id, props.entrada.cantidad - 1);
};

const quitar = () => {
  emit('quitar', props.entrada.id);
};
</script>

<template>
  <article
    class="carta-mazo"
    :class="{
      'carta-mazo--limite': entrada.cantidad >= 2
    }"
  >
    <div class="carta-mazo__info">
      <span class="carta-mazo__emoji">{{ carta.emoji }}</span>
      <span class="carta-mazo__nombre">{{ carta.nombre }}</span>
      <span class="carta-mazo__coste">Coste: {{ carta.coste }}</span>
    </div>

    <div class="carta-mazo__controles">
      <button type="button" @click="bajar">-</button>
      <span>x{{ entrada.cantidad }}</span>
      <button type="button" @click="subir">+</button>
      <button type="button" @click="quitar">Quitar</button>
    </div>

    <p v-if="entrada.cantidad >= 2" class="carta-mazo__alerta">
      Máximo de 2 copias alcanzado.
    </p>
  </article>
</template>