
<template>
  <div>
      
      <div id="map"></div>

  <div class="Search_row">
       <input v-model="searchQuery" @keyup.enter="handleSearch" type="text" placeholder="Input location" class="Input_row" />
        <div class="row"><button type="button" class="Button" @click="handleSearch">Search</button></div>
      </div>
  </div>
  
</template>



<script>
import axios from 'axios';
import locationImg from "../assets/location-sign.svg" 
export default {
  props: {
     deleteItem: {
      type: Array,
      default: () => []
    },
     apiKey: {
      type: String, 
      required: true 
    }
  },
  data() {
    return {
      map: null,
      markers: [],
      searchQuery: "",
    };
  },
  mounted() {

    const script = document.createElement("script");
    script.src = `https://maps.googleapis.com/maps/api/js?key=${this.apiKey}&libraries=places&callback=initApp`;
    script.defer = true;
    script.async = true;
    // script.addEventListener("load", this.initMap);
  // Create a promise to wait for the Google Maps API to load
  const mapLoaded = new Promise((resolve, reject) => {
    script.onload = resolve; // Resolve the promise when the script is loaded
    script.onerror = reject; // Reject the promise if there's an error loading the script
  });

  // Append the script to the document head
  document.head.appendChild(script);

  // Wait for the Google Maps API to load before calling the initMap function
  mapLoaded.then(() => {
    this.initMap(); // Call the initMap function once the API is fully loaded
  }).catch(() => {
    console.error("Error loading Google Maps API.");
  });

  },
  methods: {

    initMap() {
       this.map = new google.maps.Map(document.getElementById("map"), {
          center: { lat: 43.6607306, lng: -79.4169958 },
          zoom: 15,
        });

      this.infoWindow = new google.maps.InfoWindow();

      const imgbutton = document.createElement("img");
       imgbutton.src = locationImg; 
       imgbutton.alt = "Location"; 
       imgbutton.style.width = "28px"; 
       imgbutton.style.height = "28px"; 
       imgbutton.style.borderRadius = "50%"; 
       imgbutton.style.padding = "5px"; 
       imgbutton.style.margin = "5px 15px"; 
       imgbutton.style.backgroundColor = "#fff"; 
       imgbutton.style.cursor = "pointer"; 
     

      this.map.controls[google.maps.ControlPosition.TOP_RIGHT].push(imgbutton);

      imgbutton.addEventListener("click", () => {
          if (navigator.geolocation) {
            navigator.geolocation.getCurrentPosition(
              (position) => {
                const pos = {
                  lat: position.coords.latitude,
                  lng: position.coords.longitude,
                };
          
                this.infoWindow.setPosition(pos);
                this.infoWindow.setContent("Location found.");
                this.infoWindow.open(map);
                this.map.setCenter(pos);
                this.createMarker(pos);
              },
              () => {
                handleLocationError(true, this.infoWindow, this.map.getCenter());
              }
            );
          } else {
            // Browser doesn't support Geolocation
            handleLocationError(false, this.infoWindow, this.map.getCenter());
          }
        });

      google.maps.event.addListenerOnce(this.map, "idle", () => {
        this.removeMarkers();
      });

    },
    handleSearch() {
      if (!this.searchQuery) return; 

      var service = new google.maps.places.PlacesService(this.map);
      service.findPlaceFromQuery({ query: this.searchQuery, fields: ["name","geometry"] }, (results, status) => {
        if (status === google.maps.places.PlacesServiceStatus.OK && results && results.length > 0) {
         
     
          for (let i = 0; i < results.length; i++) {       
            this.createMarker(results[i]);
          }
        
          this.map.setCenter(results[0].geometry.location);

          const lat = results[0].geometry.location.lat();
          const lng = results[0].geometry.location.lng();
          
          this.getTimeZone(lat, lng);

           const searchResults = results.map((result) => ({
            name: result.name,
            lat: result.geometry.location.lat(),
            lng: result.geometry.location.lng(),

          }));
        
          this.$emit('searchResults', searchResults);
        }
      });
    },
  
    createMarker(place) {

      if (!place.geometry || !place.geometry.location) return;

      const marker = new google.maps.Marker({
        map: this.map, 
        position :place.geometry.location,
        lat: place.geometry.location.lat(),
        lng: place.geometry.location.lng(),
      });

      google.maps.event.addListener(marker, "click", () => {
        this.infoWindow.setContent(place.name || "");
        this.infoWindow.open(this.map); 
      });
      this.markers.push(marker);
     
    },

removeMarkers() {

  this.deleteItem.forEach(item => {
    if (item.selected) {
      const { lat, lng } = item;
      const index = this.markers.findIndex(marker => marker.getPosition().equals(new google.maps.LatLng(lat, lng)));
  
      if (index !== -1) {

        this.markers[index].setMap(null);
        this.markers[index].setVisible(false);
        this.markers[index].setPosition(null);
        this.markers[index] = null;

        this.markers.splice(index, 1);

      }
    }
  });

  },

  
  getTimeZone(lat, lng) {
   
    const url = `https://api.geoapify.com/v1/geocode/reverse?lat=${lat}&lon=${lng}&format=json&apiKey=74c70dab588e4a969dbddd7eb27d728a`;

       return fetch(url)
      .then(resp => resp.json())
      .then((result) => {
      
        if (result.results.length > 0) {
          const timezoneinfo = result.results[0].timezone;

          this.$emit('timezone', timezoneinfo);
        } else {
          console.log("No location found");
        }
      });
  },
    
    handleLocationError(browserHasGeolocation, infoWindow, pos) {
        infoWindow.setPosition(pos);
        infoWindow.setContent(
          browserHasGeolocation
            ? "Error: The Geolocation service failed."
            : "Error: Your browser doesn't support geolocation."
        );
        infoWindow.open(map);
    },


  },

   watch: {
    deleteItem: {
      handler(newValue) {
        if(this.deleteItem.length !== 0){
           this.removeMarkers();
        }
       
      },
      immediate: true, 
    },
  },


};
</script>


<style scoped>
#map {
  width: 90vw;
  height: 400px;
}

html,
body {
  height: 100%;
  margin: 0;
  padding: 0;
}


.custom-location-icon {
    width: 24px;
    height: 24px;
    background-color: #fff;
    border-radius: 12px;
    margin: 10px;
    cursor: pointer;
}

.Search_row{
  margin: 10px 0;
  display:flex;
}
.row{
     margin-left: -8px;
}
.Input_row{
    width: 40%;
    padding: 10px 20px 10px 20px;
    min-width: 100px;
    min-height: 40px;
    outline: none;
    border: none;
    border-radius: 4px;
    box-shadow: rgba(0, 0, 0, 0.3) 0px 1px 4px -1px;
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
    margin-left: 8px;
    margin-right: 8px;
    font-size: 1.8vh;
    font-weight: bold;
    white-space: nowrap;
    margin: 0 5px;
    cursor: pointer;
}
</style>