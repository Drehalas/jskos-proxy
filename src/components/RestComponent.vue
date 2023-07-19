<template>
  <div>
    <p v-if="loading">Loading NOMISMA Data...</p>
    <div v-else-if="dataLoaded">
      <h2>NOMISMA Data:</h2>
      <div v-if="nomismaData.id">
        <p>ID: {{ nomismaData.id }}</p>
      </div>
      <div v-if="nomismaData.name">
        <p>Name: {{ nomismaData.name }}</p>
      </div>
      <div v-if="location">
        <h3>Location Coordinates:</h3>
        <p>Latitude: {{ location[1] }}</p>
        <p>Longitude: {{ location[0] }}</p>
      </div>
    </div>
    <div v-else>
      <p>No NOMISMA Data fetched yet.</p>
    </div>
  </div>
</template>

<script>
import axios from "axios"

export default {
  props: {
    input: String,
  },
  data() {
    return {
      nomismaData: {},
      loading: false,
      dataLoaded: false,
    }
  },
  methods: {
    fetchData() {
      this.loading = true
      axios
        .get(this.input)
        .then((response) => {
          this.loading = false
          this.dataLoaded = true
          this.nomismaData = response.data
        })
        .catch((error) => {
          this.loading = false
          console.error("Error fetching NOMISMA Data:", error)
        })
    },
  },
  computed: {
    location() {
      return this.nomismaData?.location?.coordinates
    },
  },
}
</script>
