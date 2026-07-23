<script setup>
import { ref, computed } from 'vue';
import { defineProps, defineEmits } from 'vue';
import CartaCatalogo from './CartaCatalogo.vue';

const orden = ref('nombre'); // 'nombre' o 'coste'

const props = defineProps({
    catalogo: {
        type: Array,
        required: true,
    },
});

const emit = defineEmits(['agregar']);

// Estado de UI
const textoBusqueda = ref('');
const filtroTipo = ref('Todas');
const filtroRareza = ref('Todas');
const filtroCoste = ref('Todos');

// Lista filtrada
const cartasFiltradas = computed(() => {
    // 1. Filtrado
    const filtradas = props.catalogo.filter((carta) => {
        const nombre = carta.nombre.toLowerCase();
        const busqueda = textoBusqueda.value.toLowerCase().trim();

        const coincideNombre = nombre.includes(busqueda);
        const coincideTipo =
            filtroTipo.value === 'Todas' || carta.tipo === filtroTipo.value;
        const coincideRareza =
            filtroRareza.value === 'Todas' || carta.rareza === filtroRareza.value;
        const coincideCoste =
            filtroCoste.value === 'Todos' ||
            carta.coste === Number(filtroCoste.value);

        return coincideNombre && coincideTipo && coincideRareza && coincideCoste;
    });

    // 2. Ordenamiento
    if (orden.value === 'nombre') {
        return filtradas.slice().sort((a, b) =>
            a.nombre.localeCompare(b.nombre)
        );
    }

    if (orden.value === 'coste') {
        return filtradas.slice().sort((a, b) => a.coste - b.coste);
    }

    return filtradas;
});

const handleAgregar = (idCarta) => {
    emit('agregar', idCarta);
};
</script>

<template>
  <section class="catalogo">
    <h2>Catálogo</h2>

    <div class="catalogo__filtros">
      <input
        type="text"
        v-model="textoBusqueda"
        placeholder="Buscar por nombre..."
      />

      <select v-model="filtroTipo">
        <option value="Todas">Tipo: Todas</option>
        <option value="Criatura">Criatura</option>
        <option value="Hechizo">Hechizo</option>
        <option value="Trampa">Trampa</option>
      </select>

      <select v-model="filtroRareza">
        <option value="Todas">Rareza: Todas</option>
        <option value="Común">Común</option>
        <option value="Rara">Rara</option>
        <option value="Épica">Épica</option>
      </select>

      <select v-model="filtroCoste">
        <option value="Todos">Coste: Todos</option>
        <option value="0">0</option>
        <option value="1">1</option>
        <option value="2">2</option>
        <option value="3">3</option>
        <option value="4">4</option>
        <option value="5">5</option>
        <option value="6">6</option>
        <option value="7">7</option>
        <option value="8">8</option>
        <option value="9">9</option>
        <option value="10">10</option>
      </select>

      <select v-model="orden">
        <option value="nombre">Ordenar por nombre</option>
        <option value="coste">Ordenar por coste</option>
      </select>
    </div>

    <div class="catalogo__lista">
      <p>
        Cartas visibles: {{ cartasFiltradas.length }}
      </p>

      <p v-if="cartasFiltradas.length === 0">
        Ninguna carta coincide
      </p>

      <CartaCatalogo
        v-for="carta in cartasFiltradas"
        :key="carta.id"
        :carta="carta"
        @agregar="handleAgregar"
      />
    </div>
  </section>
</template>