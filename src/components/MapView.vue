<template>
  <div class="h-screen w-full flex">
    <div id="map" class="h-full flex-grow"></div>
  </div>
</template>

<script setup>
import {ref, onMounted, defineEmits} from "vue";
import L from "leaflet";
import "leaflet-fullscreen";
import "leaflet/dist/leaflet.css";
import "leaflet-fullscreen/dist/leaflet.fullscreen.css";
import * as esriLeaflet from "esri-leaflet";

const map = ref(null);
const emit = defineEmits(["falla-seleccionada"]);

const specialSectionIconBase64 = "data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACoAAAAqCAYAAADFw8lbAAAACXBIWXMAAA7EAAAOxAGVKw4bAAAEzklEQVRYhe2Zv2/aaBjHPyDSClVpe1Y9mEpdysQYGlaGDolUtxNDcnPLCaEM+QPiEPIHdIgiq2TqcmRgOF1SiQ4dvDqlww2evJ0wgyVfqyqKaCy4wTZHqAFD6FWn63eC930NH55f7/s+JPiPKPG9AaLqB+iidW3QNjwFsjGQ/aHs0HSnD60YWEAtBa15v2cuUAuyfXjqw2XjgshSOkNcEIkL4mBdz7GlS9OQXdMAKFrecM2FvQce/LcDtaAIvIoByfUCN1bz3Mzlpz7X1TXO6yquaRQTUGzD3n2oLBzUt2IFkJPrBZJrBRLpTNTHuZnzfpBrGpzXVbq6tmuB7MKzKNaNBOrH4auldEa6tVmKZMGxX5jOcGfnANc0+Li/lU04dtuCR9Pidypo24vF3xPpDLfLykxWnAYsvDzG2d6g59jvp8FOBLW8DH6VSGe4u3NwJVEWobggcu/1O5ztDVzTmAg7EbQPlaV0RrpdVhYOOay7OweBZU+AVNiasaB+dsu3NksLc/c4xQWR5bLCp/0tqQ2VsGoQChq4PLlemJo4HcemWldRy8q1YIOq0NW1XaKC+sWc5Fph7AcfNRse6F82p7rGykPvvZzLI80ZJstlha7+GAuKKahNBY35tXKcy4+aDUqH1StjwfvSIbgnf8wFGhdEkusFLpqNCtNAg737xur8tfI6Sq4VuGg2pDY8vQ8nwXiYRbNxQZypqEuCSMexQ+c6jj1TKARejIF0ZXx0YQzkpRmyXM7lebKap1pXB7ClwypqWaFaV/lgGqhlZWZY1zSKDLk/NEZnqZkr6Qwv1gscvW0MQDuOzVGzgfSTyPM1b+7FWiEy7JIHeqWejnV9VJ3q2qACBJIEkWw6gySItEyDU10DmAmWaa6PIjmXp1r34vKDd9YcSPm5hLJZGlQGtayQmhDDYQoz1FygkiDy5+t3VOsq1V9VwAsBZbOEnMtTOqwOrPzmTOO5X4/nra/jQDs9x5ZCxr+Sslli5WGG0mEV/eWx97AfnwHYqa5562ZI0J5n/c5E0D60Lk1DHh0fp5QgXrFUEI8B6Iv1AtW6ym87B5FBL02D/sgpKqw8We5I3M0iz/Xe65Qg8sE0ZnJ5z7FxTYPYyKk/zPU1oNjVtblO8kdvG4MNILCunMtzqmvIEe9WQxzjQVPQsoDzujoTaLDXj5Yq+Ccc3pxpPFnNTwT+cqYBdEYP0OOyvuaaRtE1jaln0aBmrjz01k077mUnfJ5rGoFFT0bnQkFd2EtA8byucmdKEkiCeO2zaKDzugpettdG50JBH4DVhr2uru1Gseoi1NW1gTXD7k1jC/59qFggf9zfygovj7/pnck1DT57Md5KwS9haybuTC48Szh229ne4N7rd9+CcdCQ6Dl2qw9749ZNBH0AlgWPeo793tneWPiVeahrQt9r8XyVRJFAYVCuHrmm8d7Z3mC5rFyrUxKoq2t8PqwOLDkJMhLoMGzPsU8+7W9JN3N5lue86w9bEYgEGRk0gAVSbah0dW23qz8marOs59h0dY0vZ4PM7uBld2jiXAs0kN8cqFhQvGg2KhfNhgTe9SG4wgSW7jk2l6bB0Nmhg2e9mZu6c3ec/Xt3zW+iSf5OluLqyfz7dpyHNRRfX+0mi9T/58+Gf0t/A+StDeWZzrw7AAAAAElFTkSuQmCC";

let fallasPorSector = [];
const capasPorSector = {};

const getColorForSection = (seccion) => {
  if (!seccion) return "#000000";
  const sectionNumber = parseInt(seccion.replace(/\D/g, ""));
  if (isNaN(sectionNumber)) return "#000000";
  const red = Math.min(255, 255 - (sectionNumber - 1) * 30);
  const green = Math.min(255, (sectionNumber - 1) * 30);
  return `rgb(${red}, ${green}, 0)`;
};

const generateNumberIcon = (seccion, isLarge = false) => {
  const size = isLarge ? 48 : 24;
  const canvas = document.createElement("canvas");
  canvas.width = size;
  canvas.height = size;
  const ctx = canvas.getContext("2d");

  ctx.fillStyle = getColorForSection(seccion);
  ctx.beginPath();
  ctx.arc(size / 2, size / 2, size / 2, 0, Math.PI * 2);
  ctx.fill();

  ctx.fillStyle = "#FFFFFF";
  ctx.font = `bold ${size / 2}px Arial`;
  ctx.textAlign = "center";
  ctx.textBaseline = "middle";
  ctx.fillText(seccion, size / 2, size / 2);

  return canvas.toDataURL();
};

const normalizeSectorName = (text) => {
  return text
      ? text
          .toLowerCase()
          .normalize("NFD")
          .replace(/[\u0300-\u036f]/g, "")
          .replace(/\s+/g, " ")
          .trim()
      : "sin sector";
};

const fetchAllFeatures = async (layer) => {
  const query = layer.query();
  let allFeatures = [];

  const getPage = async (query, response = null) => {
    return new Promise((resolve, reject) => {
      query.run((error, featureCollection, nextResponse) => {
        if (error) return reject(error);
        allFeatures = [...allFeatures, ...featureCollection.features];
        if (nextResponse?.properties?.exceededTransferLimit) {
          query.next(nextResponse, (err, nextFeatureCollection, nextNextResponse) => {
            if (err) return reject(err);
            resolve(getPage(query, nextNextResponse));
          });
        } else {
          resolve(allFeatures);
        }
      });
    });
  };

  return getPage(query);
};

const setupMap = () => {

  const baseLayers = {
    "OpenStreetMap": L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
      attribution: "Map data © OpenStreetMap contributors",
    }),
    "IGN Base": L.tileLayer.wms("https://www.ign.es/wms-inspire/ign-base?", {
      layers: "IGNBaseTodo",
      format: "image/png",
      transparent: true,
      opacity: 1,
      attribution: '© <a href="http://www.ign.es/">Instituto Geográfico Nacional de España</a>',
    }),
    "Ortofoto PNOA": L.tileLayer.wms("https://www.ign.es/wms-inspire/pnoa-ma?", {
      layers: "OI.OrthoimageCoverage",
      format: "image/jpeg",
      attribution: "PNOA - © Instituto Geográfico Nacional de España",
    }),
    "Ortofoto GVA 2024": L.tileLayer.wms("https://terramapas.icv.gva.es/0202_2024CVAL0025?SERVICE=WMS", {
      layers: "2024CVAL0025_RGB",
      format: "image/png",
      attribution: '&copy; <a href="https://visor.gva.es/visor/?capasids=Orto_2024;&nodoDesplegado=Orto_2024&idioma=es">Institut Cartogràfic Valencià</a>',
    }),
  };

  map.value = L.map("map", {
    center: [39.470239, -0.376805],
    zoom: 13,
    fullscreenControl: true,
    layers: [baseLayers["OpenStreetMap"]],
  });

  L.control.layers(baseLayers, {}).addTo(map.value);
};

const setupFallasLayer = async () => {

  const fallasLayer = esriLeaflet.featureLayer({
    url: "https://geoportal.valencia.es/server/rest/services/CulturaFestiva/FallasPublicoV2/MapServer/2"
  });

  try {
    const allFeatures = await fetchAllFeatures(fallasLayer);
    console.log("Total de Features obtenidas:", allFeatures.length);

    fallasPorSector = allFeatures.reduce((acc, falla) => {
      const sectorOriginal = falla.properties.sector || "Sin Sector";
      const sectorNormalizado = normalizeSectorName(sectorOriginal);
      if (!acc[sectorNormalizado]) {
        acc[sectorNormalizado] = {
          nombreOriginal: sectorOriginal,
          fallas: [],
        };
      }
      acc[sectorNormalizado].fallas.push(falla);
      return acc;
    }, {});

    Object.entries(fallasPorSector).forEach(([sectorNormalizado, datosSector]) => {
      const {nombreOriginal, fallas} = datosSector;
      const layerGroup = L.layerGroup();

      fallas.forEach(falla => {
        if (falla.geometry && Array.isArray(falla.geometry.coordinates)) {
          const [lon, lat] = falla.geometry.coordinates;
          if (typeof lat === 'number' && typeof lon === 'number') {
            const seccion = falla.properties.seccion || "Sin Sección";
            const iconUrl = seccion === "E" ? specialSectionIconBase64 : generateNumberIcon(seccion);
            const marker = L.marker([lat, lon], {
              icon: L.icon({
                iconUrl: iconUrl,
                iconSize: seccion === "E" ? [48, 48] : [24, 24],
                iconAnchor: seccion === "E" ? [24, 48] : [12, 24],
                popupAnchor: [1, -34],
              }),
            });

            marker.on("click", () => {
              emit("falla-seleccionada", falla.properties);
            });

            layerGroup.addLayer(marker);
          }
        }
      });

      capasPorSector[sectorNormalizado] = layerGroup;
    });

    const capasConNombresOriginales = Object.entries(fallasPorSector).reduce((acc, [sectorNormalizado, datosSector]) => {
      acc[datosSector.nombreOriginal] = capasPorSector[sectorNormalizado];
      return acc;
    }, {});

    const layersControl = L.control.layers(null, capasConNombresOriginales, {collapsed: true}).addTo(map.value);

    const layersControlContainer = layersControl.getContainer();
    const customIcon = document.createElement('div');
    customIcon.innerHTML = 'Sector';
    customIcon.style.backgroundColor = '#4CAF50';
    customIcon.style.color = 'white';
    customIcon.style.width = '42px';
    customIcon.style.height = '42px';
    customIcon.style.display = 'flex';
    customIcon.style.alignItems = 'center';
    customIcon.style.justifyContent = 'center';
    customIcon.style.fontWeight = 'bold';

    const defaultIcon = layersControlContainer.querySelector('.leaflet-control-layers-toggle');
    if (defaultIcon) {
      defaultIcon.replaceWith(customIcon);
    }

    Object.values(capasPorSector).forEach(layer => layer.addTo(map.value));

  }
  catch (error) {
    console.error("Error obteniendo features:", error);
  }
};

onMounted(() => {
  setupMap();
  setupFallasLayer();
});
</script>

<style>
#map {
  height: 100vh;
  width: 100%;
}

.custom-layer-icon {
  margin-right: 5px;
}
</style>