<script setup>
import { ref, computed, watch } from 'vue';
import Catalogo from './components/Catalogo.vue';
import PanelMazo from './components/PanelMazo.vue';

// 1. Estado base
const catalogo = ref([
  { id: 1, nombre: 'Goblin', emoji: '☘', tipo: 'Criatura', coste: 1, rareza: 'Común' },
  { id: 2, nombre: 'Dragón de fuego', emoji: '🐉', tipo: 'Criatura', coste: 8, rareza: 'Épica' },
  { id: 3, nombre: 'Bola de fuego', emoji: '🔥', tipo: 'Hechizo', coste: 4, rareza: 'Rara' },
]);

const mazo = ref([]); // [{ id, cantidad }]

// 2. Derivados del mazo
const totalCartas = computed(() =>
  mazo.value.reduce((acc, entrada) => acc + entrada.cantidad, 0)
);

const costeMedio = computed(() => {
  if (totalCartas.value === 0) return 0;
  const costeTotal = mazo.value.reduce((acc, entrada) => {
    const carta = catalogo.value.find((c) => c.id === entrada.id);
    return carta ? acc + carta.coste * entrada.cantidad : acc;
  }, 0);
  return Number((costeTotal / totalCartas.value).toFixed(2));
});

// 3. Funciones de negocio (agregar/ajustar/quitar)
const agregarCartaAlMazo = (idCarta) => {
  const entradaExistente = mazo.value.find((entrada) => entrada.id === idCarta);

  const totalActual = mazo.value.reduce((acc, e) => acc + e.cantidad, 0);
  if (totalActual >= 30) {
    return;
  }

  if (!entradaExistente) {
    mazo.value.push({ id: idCarta, cantidad: 1 });
  } else if (entradaExistente.cantidad < 2) {
    entradaExistente.cantidad += 1;
  } else {
    // límite de 2 copias
  }
};

const ajustarCantidadCarta = (idCarta, nuevaCantidad) => {
  const entrada = mazo.value.find((e) => e.id === idCarta);
  if (!entrada) return;

  if (nuevaCantidad <= 0) {
    mazo.value = mazo.value.filter((e) => e.id !== idCarta);
    return;
  }

  if (nuevaCantidad > 2) {
    entrada.cantidad = 2;
  } else {
    entrada.cantidad = nuevaCantidad;
  }

  const totalActual = mazo.value.reduce((acc, e) => acc + e.cantidad, 0);
  if (totalActual > 30) {
    entrada.cantidad -= (totalActual - 30);
  }
};

const quitarCartaDelMazo = (idCarta) => {
  mazo.value = mazo.value.filter((e) => e.id !== idCarta);
};

// 4. Estado y funciones de mazos guardados
const nombreMazo = ref('');
const mazosGuardados = ref([]); // [{ nombre, mazo }]

const guardarMazoActual = () => {
  const nombre = nombreMazo.value.trim();
  if (!nombre) return;
  if (mazo.value.length === 0) return;

  const existente = mazosGuardados.value.find((item) => item.nombre === nombre);
  const copiaMazo = JSON.parse(JSON.stringify(mazo.value));

  if (existente) {
    existente.mazo = copiaMazo;
  } else {
    mazosGuardados.value.push({ nombre, mazo: copiaMazo });
  }

  nombreMazo.value = '';
};

const cargarMazoGuardado = (nombre) => {
  const item = mazosGuardados.value.find((m) => m.nombre === nombre);
  if (!item) return;
  mazo.value = JSON.parse(JSON.stringify(item.mazo));
};

// 5. Persistencia (watch + carga inicial)
watch(
  mazo,
  (nuevo) => {
    localStorage.setItem('mazo', JSON.stringify(nuevo));
  },
  { deep: true }
);

watch(
  mazosGuardados,
  (nuevo) => {
    localStorage.setItem('mazosGuardados', JSON.stringify(nuevo));
  },
  { deep: true }
);

const guardado = localStorage.getItem('mazo');
if (guardado) {
  mazo.value = JSON.parse(guardado);
}

const guardadosLista = localStorage.getItem('mazosGuardados');
if (guardadosLista) {
  mazosGuardados.value = JSON.parse(guardadosLista);
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

      <PanelMazo
        :catalogo="catalogo"
        :mazo="mazo"
        @ajustar="ajustarCantidadCarta"
        @quitar="quitarCartaDelMazo"
      />
    </section>

    <section class="mazos-guardados">
      <h2>Mazos guardados</h2>

      <form @submit.prevent="guardarMazoActual">
        <input
          type="text"
          v-model="nombreMazo"
          placeholder="Nombre para este mazo..."
        />
        <button type="submit">Guardar mazo</button>
      </form>

      <ul>
        <li
          v-for="item in mazosGuardados"
          :key="item.nombre"
        >
          <button type="button" @click="cargarMazoGuardado(item.nombre)">
            Cargar "{{ item.nombre }}"
          </button>
        </li>
      </ul>
    </section>
  </main>
</template>