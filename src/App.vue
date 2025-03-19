<script setup>
import { ref } from "vue";
import MapView from "./components/MapView.vue";
import FallaInfo from "./components/FallaInfo.vue";

const selectedFalla = ref(null);

const closeFallaInfo = () => {
  selectedFalla.value = null;
};
</script>

<template>
  <div class="flex flex-col md:flex-row h-screen">

    <!-- Mapa -->
    <div class="w-full flex-1">
      <MapView @falla-seleccionada="selectedFalla = $event" />
    </div>

    <!-- Info de la falla -->
    <FallaInfo
        v-if="selectedFalla"
        :falla="selectedFalla"
        @close="closeFallaInfo"
        class="w-full md:w-1/4 bg-white shadow-md z-10 absolute md:relative top-0 left-0"
    />
  </div>
</template>
