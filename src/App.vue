<template>
  <div v-if="uri">
    <div v-if="Object.keys(item).length > 0">
      <concept-scheme-view
        v-if="jskosType=='ConceptScheme'"
        :item="item"
        :registry="registry"
        @select="selectItem" />
      <item-view
        v-else
        :item="item"
        :registry="registry"
        @select="selectItem" />
      <div v-if="location">
        <h2>Location Coordinates:</h2>
        <p>Latitude: {{ location[1] }}</p>
        <p>Longitude: {{ location[0] }}</p>
      </div>
      <input v-model="inputData" type="text" placeholder="Enter the URL">
      <button @click="fetchData">Fetch NOMISMA Data</button>
      <rest-component :input="inputData" ref="restComponentRef" />
    </div>


    <div v-else>
      <!-- 404: TODO show search form to search vocabularies -->
      <div>
        <h2>Search Vocabularies</h2>
        <input v-model="searchTerm" type="text" placeholder="Enter search term">
        <button @click="searchVocabularies">Search</button>
      </div>
      <div v-if="searchResults.length > 0">
        <h2>Search Results:</h2>
        <ul>
          <li v-for="result in searchResults" :key="result.uri">
            <a :href="result.uri" @click.prevent="selectItem(result)">{{ result.prefLabel.en }}</a>
          </li>
        </ul>
      </div>
      <div v-else>
        <p>No search results yet.</p>
      </div>
    </div>


  </div>
  <div v-else-if="backend">
    <concept-scheme-selection
      :registry="registry"
      @select="selectItem" />
  </div>
  <rest-component ref="restComponentRef" />
</template>

<script setup>
import ConceptSchemeSelection from "./components/ConceptSchemeSelection.vue"
import ConceptSchemeView from "./components/ConceptSchemeView.vue"
import ItemView from "./components/ItemView.vue"
import RestComponent from "./components/RestComponent.vue"
import { link } from "../lib/utils.js"
import { reactive, ref } from "vue"
import jskos from "jskos-tools"
import { cdk } from "cocoda-sdk"

// current item
const uri = [...document.getElementsByTagName("link")].find(e => e.rel=="self")?.href
const item = reactive(JSON.parse(document.getElementById("item")?.textContent||"{}"))
item.location = undefined

// configuration
const body = document.getElementsByTagName("body")[0]
const namespace = new URL(body.dataset.namespace)
const backend = body.dataset.backend

var registry
if (backend.match(/^https?:/)) {
  registry = cdk.initializeRegistry({
    provider: "ConceptApi",
    status: backend+"status",
  })
}

const jskosType = jskos.guessObjectType(item)

const selectItem = item => {
  if (item.uri) {
    location.href = link(item.uri, namespace)
  }
}

const restComponentRef = ref(null)

const inputData = ref("http://localhost:3555/id/cotiaeum")

const fetchData = () => {
  if (restComponentRef.value) {
    restComponentRef.value.fetchData()
  }
}
</script>
