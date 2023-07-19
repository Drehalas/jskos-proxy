<!-- RestComponent.vue -->

<template>
  <div>
    <p v-if="loading">
      Loading NOMISMA Data...
    </p>
    <div v-else-if="dataLoaded">
      <h2>NOMISMA Data:</h2>
      <p>ID: {{ nomismaData.id }}</p>
      <p>Name: {{ nomismaData.name }}</p>
      <p>Location: {{ nomismaData.location }}</p>
    </div>
    <div v-else>
      <p>No NOMISMA Data fetched yet.</p>
    </div>
  </div>
</template>

<script>
import axios from "axios"

export default {
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
      // Simulate fetching data from a NOMISMA API
      axios.get("http://localhost:3555/id/augusta")
        .then(response => {
          this.loading = false
          this.dataLoaded = true
          // Assume the response contains the NOMISMA data
          this.nomismaData = response.data
        })
        .catch(error => {
          this.loading = false
          console.error("Error fetching NOMISMA Data:", error)
        })
    },
  },
}
</script>
