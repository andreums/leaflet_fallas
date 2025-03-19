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
  <div class="flex h-screen">
    <div class="w-3/4 h-full">
      <MapView @falla-seleccionada="selectedFalla = $event" />
    </div>
    <FallaInfo v-if="selectedFalla" :falla="selectedFalla" @close="closeFallaInfo" />
  </div>
</template>