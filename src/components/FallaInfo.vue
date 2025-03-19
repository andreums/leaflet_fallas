<script setup>
import { defineProps, defineEmits } from "vue";

const props = defineProps({
  falla: Object,
});

const emit = defineEmits(["close"]);

// Icono base64 para la Sección Especial
const specialSectionIconBase64 =
    "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACoAAAAqCAYAAADFw8lbAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAEzklEQVRYhe2Zv2/aaBjHPyDSClVpe1Y9mEpdysQYGlaGDolUtxNDcnPLCaEM+QPiEPIHdIgiq2TqcmRgOF1SiQ4dvDqlww2evJ0wgyVfqyqKaCy4wTZHqAFD6FWn63eC930NH55f7/s+JPiPKPG9AaLqB+iidW3QNjwFsjGQ/aHs0HSnD60YWEAtBa15v2cuUAuyfXjqw2XjgshSOkNcEIkL4mBdz7GlS9OQXdMAKFrecM2FvQce/LcDtaAIvIoByfUCN1bz3Mzlpz7X1TXO6yquaRQTUGzD3n2oLBzUt2IFkJPrBZJrBRLpTNTHuZnzfpBrGpzXVbq6tmuB7MKzKNaNBOrH4auldEa6tVmKZMGxX5jOcGfnANc0+Li/lU04dtuCR9Pidypo24vF3xPpDLfLykxWnAYsvDzG2d6g59jvp8FOBLW8DH6VSGe4u3NwJVEWobggcu/1O5ztDVzTmAg7EbQPlaV0RrpdVhYOOay7OweBZU+AVNiasaB+dsu3NksLc/c4xQWR5bLCp/0tqQ2VsGoQChq4PLlemJo4HcemWldRy8q1YIOq0NW1XaKC+sWc5Fph7AcfNRse6F82p7rGykPvvZzLI80ZJstlha7+GAuKKahNBY35tXKcy4+aDUqH1StjwfvSIbgnf8wFGhdEkusFLpqNCtNAg737xur8tfI6Sq4VuGg2pDY8vQ8nwXiYRbNxQZypqEuCSMexQ+c6jj1TKARejIF0ZXx0YQzkpRmyXM7lebKap1pXB7ClwypqWaFaV/lgGqhlZWZY1zSKDLk/NEZnqZkr6Qwv1gscvW0MQDuOzVGzgfSTyPM1b+7FWiEy7JIHeqWejnV9VJ3q2qACBJIEkWw6gySItEyDU10DmAmWaa6PIjmXp1r34vKDd9YcSPm5hLJZGlQGtayQmhDDYQoz1FygkiDy5+t3VOsq1V9VwAsBZbOEnMtTOqwOrPzmTOO5X4/nra/jQDs9x5ZCxr+Sslli5WGG0mEV/eWx97AfnwHYqa5562ZI0J5n/c5E0D60Lk1DHh0fp5QgXrFUEI8B6Iv1AtW6ym87B5FBL02D/sgpKqw8We5I3M0iz/Xe65Qg8sE0ZnJ5z7FxTYPYyKk/zPU1oNjVtblO8kdvG4MNILCunMtzqmvIEe9WQxzjQVPQsoDzujoTaLDXj5Yq+Ccc3pxpPFnNTwT+cqYBdEYP0OOyvuaaRtE1jaln0aBmrjz01k077mUnfJ5rGoFFT0bnQkFd2EtA8byucmdKEkiCeO2zaKDzugpettdG50JBH4DVhr2uru1Gseoi1NW1gTXD7k1jC/59qFggf9zfygovj7/pnck1DT57Md5KwS9haybuTC48Szh229ne4N7rd9+CcdCQ6Dl2qw9749ZNBH0AlgWPeo793tneWPiVeahrQt9r8XyVRJFAYVCuHrmm8d7Z3mC5rFyrUxKoq2t8PqwOLDkJMhLoMGzPsU8+7W9JN3N5lue86w9bEYgEGRk0gAVSbah0dW23qz8marOs59h0dY0vZ4PM7uBld2jiXAs0kN8cqFhQvGg2KhfNhgTe9SG4wgSW7jk2l6bB0Nmhg2e9mZu6c3ec/Xt3zW+iSf5OluLqyfz7dpyHNRRfX+0mi9T/58+Gf0t/A+StDeWZzrw7AAAAAElFTkSuQmCC";

// Función para generar un color basado en la sección
const getColorForSection = (seccion) => {
  const sectionNumber = parseInt(seccion.replace(/\D/g, ""));
  if (isNaN(sectionNumber)) return "#000000"; // Si no es un número, devolver negro

  // Escala de rojo (1B) a verde (8)
  const red = Math.min(255, 255 - (sectionNumber - 1) * 30);
  const green = Math.min(255, (sectionNumber - 1) * 30);
  return `rgb(${red}, ${green}, 0)`;
};

// Función para generar un icono con un número en formato base64
const generateNumberIcon = (seccion, isLarge = false) => {
  const size = isLarge ? 48 : 24; // Tamaño doble para "Primera A" y "Especial"
  const canvas = document.createElement("canvas");
  canvas.width = size;
  canvas.height = size;
  const ctx = canvas.getContext("2d");

  // Fondo del icono
  ctx.fillStyle = getColorForSection(seccion); // Color basado en la sección
  ctx.beginPath();
  ctx.arc(size / 2, size / 2, size / 2, 0, Math.PI * 2);
  ctx.fill();

  // Número en el centro
  ctx.fillStyle = "#FFFFFF"; // Texto blanco
  ctx.font = `bold ${size / 2}px Arial`;
  ctx.textAlign = "center";
  ctx.textBaseline = "middle";
  ctx.fillText(seccion, size / 2, size / 2);

  return canvas.toDataURL();
};
</script>

<template>
  <div class="w-1/4 h-full bg-gray-100 p-4 relative">
    <button @click="emit('close')" class="absolute top-2 right-2 bg-red-500 text-white rounded-full w-6 h-6 flex items-center justify-center">X</button>
    <div v-if="falla" class="mt-4 p-4 bg-white shadow-md rounded-lg">
      <h2 class="text-xl font-bold mb-3">Falla {{ falla.nombre }}</h2>
      <p><strong>Año de fundación:</strong> {{ falla.anyo_fundacion }}</p>
      <p><strong>Artista:</strong> {{ falla.artista }}</p>
      <p><strong>Distintivo:</strong> {{ falla.distintivo }}</p>
      <p><strong>Fallera:</strong> {{ falla.fallera }}</p>
      <p><strong>Lema:</strong> {{ falla.lema }}</p>
      <p><strong>Presidente:</strong> {{ falla.presidente }}</p>
      <p><strong>Sector:</strong> {{ falla.sector }}</p>
      <div v-if="falla.seccion === 'E'" class="mt-2 flex items-center">
        <img
            :src="specialSectionIconBase64"
            alt="Falla Sección Especial"
            class="w-12 h-12 inline-block"
        />
        <span class="ml-2 font-bold text-red-600">Sección Especial</span>
      </div>
      <div v-else-if="falla.seccion === '1A'" class="mt-2 flex items-center">
        <img
            :src="generateNumberIcon(falla.seccion, true)"
        :alt="`Falla Sección ${falla.seccion}`"
        class="w-12 h-12 inline-block"
        />
        <span class="ml-2 font-bold">Sección {{ falla.seccion }}</span>
      </div>
      <div v-else class="mt-2 flex items-center">
        <img
            :src="generateNumberIcon(falla.seccion)"
            :alt="`Falla Sección ${falla.seccion}`"
            class="w-6 h-6 inline-block"
        />
        <span class="ml-2 font-bold">Sección {{ falla.seccion }}</span>
      </div>
      <div v-if="falla.boceto" class="mt-4">
        <img :src="falla.boceto" alt="Boceto de la Falla" class="w-full rounded-lg shadow-md" />
      </div>
    </div>
    <p v-else>Selecciona una Falla en el mapa</p>
  </div>
</template>

<style scoped>
.w-1\/4 {
  width: 25%;
}
</style>