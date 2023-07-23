
<template>
  <div>
    <button class="Button" @click="deleteSelected">Delete Selected</button>
    <table>
      <thead>
        <tr>
          <th>Select</th>
          <th>Location Name</th>
          <th>Latitude</th>
          <th>Longitude</th>
          <th>Time</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="location in paginatedLocations" :key="location.name">
          <td>
            <input type="checkbox" v-model="location.selected"/>
          </td>
          <td>{{ location.name }}</td>
          <td>{{ location.lat }}</td>
          <td>{{ location.lng }}</td>
          <td>{{ getTime(location.timezone) }}</td>

        </tr>
      </tbody>
    </table>
    
    <div>
      <button class="Button" @click="prevPage" :disabled="currentPage === 1">Previous</button>
      <span>Page {{ currentPage }} of {{ totalPages }}</span>
      <button class="Button" @click="nextPage" :disabled="currentPage === totalPages">Next</button>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    locations: Array,
    timezoneTable: Object
  },
   data() {
    return {  
      currentPage: 1,
      pageSize: 10,
      storedLocations: [],
    };
  },
  created() {
   this.storedLocations = this.locations.slice();
  },
  computed: {
    totalPages() {
      return Math.ceil(this.storedLocations.length / this.pageSize);
    },
     paginatedLocations() {
     const startIndex = (this.currentPage - 1) * this.pageSize;
      const endIndex = startIndex + this.pageSize;
      const currentLocations = this.storedLocations.slice(startIndex, endIndex);

      let timezoneData = JSON.parse(JSON.stringify(this.timezoneTable))

      const paginatedWithTimezone = currentLocations.map((location) => {
        return {
          ...location,
          timezone: timezoneData ? timezoneData.name : null,
        };
      });

      return paginatedWithTimezone;
    },
  },
  methods: {
    deleteSelected() {
     const selectedLocations = this.paginatedLocations.filter((location) => location.selected);
     this.storedLocations = this.paginatedLocations.filter((location) => !location.selected);
     console.log("selectedLocations" ,selectedLocations)
     console.log("this.storedLocations",   this.storedLocations)
     this.$emit("delete", selectedLocations);
    },
    prevPage() {
      this.currentPage--;
    },
    nextPage() {
      this.currentPage++;
    },
    getTime(value){
       const now = new Date();
       return now.toLocaleString("en-US", { timeZone: value });

    }
  },
  
  watch: {
    locations(newValue) {
     this.storedLocations = this.storedLocations.concat(newValue);
    },
  },

};
</script>

<style>
table {
  width: 100%;
  border-collapse: collapse;
}
th, td {
  border: 1px solid #ccc;
  padding: 8px;
}


.Button{
    min-width: 120px;
    min-height: 45px;
    padding: 8px;
    background-color: #303030;
    border-radius: 4px;
    color: #fff;
    border: none;
    outline: none;
    margin: 8px;
    font-size: 1.8vh;
    font-weight: bold;
    white-space: nowrap;
   cursor: pointer;
}
</style>
