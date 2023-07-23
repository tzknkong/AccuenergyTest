<template>
  <div>
    <MapComponent :apiKey="apiKey" @timezone="timezoneResult" @searchResults="searchResult" :deleteItem="deleteLocations" />
   <TableComponent :locations="searchedLocations" @delete="deleteSelected" :timezoneTable="timezoneinfo" />
    <!-- <div v-if="localTime">Local Time: {{ localTime }}</div>  -->
  </div>
</template>

<script>
import MapComponent from './components/Map.vue'
import TableComponent from './components/Table.vue'

export default {
  components: {
    MapComponent,
    TableComponent
  },
  data() {
    return {
      apiKey:"YOUR_GOOGLE_MAPS_API_KEY",
      localTime: null,
      searchedLocations: [],
      timezoneinfo: [],
      deleteLocations : [],
    }
  },
  methods: {
    searchResult(results) {
      this.searchedLocations = results;
    },
    timezoneResult(info) {
      this.timezoneinfo = info;
    },
    deleteSelected(selectedLocations) {
      this.deleteLocations = selectedLocations;
     
    }
  }
}
</script>
