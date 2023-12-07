<template>
  <div id="map" class="map-container" ref="mapElement">
  </div>

  <button class="get-positions" @click="getCoords">
    Nuevas ubicaciones
  </button>


</template>

<script>
import mapboxgl from 'mapbox-gl'
import axios from 'axios'
import { onMounted, ref } from 'vue';

mapboxgl.accessToken = 'pk.eyJ1IjoiYWxkb2oiLCJhIjoiY2xwdmZlOHhjMDQ4ZTJycWxqeDN3M2N1MyJ9.Td854VxriETg-gNfzvu0iA';

export default {
  name: 'Map',
  setup() {
    const mapElement = ref()
    const markers = ref([])

    const initializeMap = () => {
      mapElement.value = new mapboxgl.Map({
        container: 'map',
        style: 'mapbox://styles/mapbox/streets-v11',
        center: [-99.1453313, 19.439144], // [longitud, latitud]
        zoom: 10,
      });
    }

    const removeMarkers = () => {
      for (const marker of markers.value) {
        marker.remove();
      }
    }

    const getCoords = async () => {
      const { data } = await axios.get('http://localhost:3000/random-position')
      const positions = data.data
      const lastCoord = positions[positions.length -1]
      removeMarkers()
      for (const position of positions) {
        setMarker(position)
      }
      // mapElement.value.setCenter([lastCoord.longitud, lastCoord.latitud])
    }

    const setMarker = (coords) => {
      const newPosition = [coords.longitud, coords.latitud]
      const marker = new mapboxgl.Marker()
        .setLngLat(newPosition)
        .setLngLat(newPosition)
        .addTo(mapElement.value)
        markers.value.push(marker)
    }

    onMounted(() => {
      initializeMap()
      setTimeout(() => {
        getCoords()
      }, 1000);
    })

    return {
      mapElement,
      getCoords,
    }
  }
}
</script>

<style scoped>
.map-container {
  height: 90vh;
}

.get-positions {
  position: absolute;
  top: 20px;
  left: 20px;
  padding: 16px 20px;
  background-color: teal;
  border: none;
  color: white;
  border-radius: 12px;
}

.get-positions:hover {
  background-color: rgb(55, 182, 188);
}
</style>