<template>
  <div>
    <h3 v-if="hasGeolocation">Geolocation:</h3>
    <div v-if="hasGeolocation">
      <div id="map" style="height: 400px;"></div>
    </div>
    <div v-else>
      <p>No geolocation data available.</p>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue"

import L from "leaflet" // Import Leaflet library

const props = defineProps({ item: Object })
const mapRef = ref(null)
const hasGeolocation = ref(false)

// Method to initialize the map
const initializeMap = () => {
  if (mapRef.value) {
    // Get the coordinates from the item.location object
    const { coordinates } = props.item.location
    const [latitude, longitude] = coordinates

    // Initialize the Leaflet map
    const map = L.map(mapRef.value).setView([latitude, longitude], 14)
    L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
      attribution: "&copy; OpenStreetMap contributors",
    }).addTo(map)

    // Add a marker for the geolocation
    L.marker([latitude, longitude]).addTo(map)

    // Set the flag indicating geolocation data is available
    hasGeolocation.value = true
  }
}

// Mount the map on component initialization
onMounted(() => {
  initializeMap()
})
</script>

<style>
/* Add any custom styles for the map container here */
#map {
  width: 100%;
}
</style>
