<template>
  <div class="flex flex-col h-screen max-h-screen">
    <!-- Search / Results -->
    <div class="z-20 flex justify-center relative bg-hero-pattern bg-cover px-4 pt-8 pb-32">
      <!-- Search Input -->
      <div class="w-full max-w-screen-sm">
        <h1 class="text-white text-center text-3xl pb-4">IP Address Tracker</h1>
        <div class="flex">
          <input v-model="queryIP" class="flex-1 py-3 px-2 rounded-tl-md rounded-bl-md focus:outline-none" type="text"
            placeholder="Search for any IP address or leave empty to get your ip info" />
          <i @click="getIpinfo" class="cursor-pointer
          bg-black
          text-white
          px-4
          rounded-tr-md
          rounded-br-md
          flex
          items-center
          fas fa-chevron-right"></i>
        </div>
      </div>
        <!-- IP Info -->
      <IPInfo v-if="IpInfo" :IpInfo='IpInfo' />
    </div>
     <!-- Map -->
    <div id="mapid" class="h-full z-10"></div>
  </div>
</template>

<script>
import IPInfo from "../components/IPInfo.vue";
import leaflet from "leaflet";
import {onMounted,ref} from "vue";
import axios from "axios";
  export default {
    name: 'Home',
    components: { IPInfo },
    setup() {
      let CurrentMap;
      const queryIP=ref("");
      const IpInfo=ref(null);
      onMounted(()=>{
        CurrentMap = leaflet.map('mapid').setView([42.5145, -83.0147], 13);
        leaflet.tileLayer('https://api.mapbox.com/styles/v1/{id}/tiles/{z}/{x}/{y}?access_token=pk.eyJ1IjoidGFyaWt1bDE1IiwiYSI6ImNrdDY3aWs4eTBmb3Eybm11aDFkOWRtY2wifQ.3xMdV8OfOGKDOxtrZ93gWw', {
    attribution: 'Map data &copy; <a href="https://www.openstreetmap.org/copyright">OpenStreetMap</a> contributors, Imagery © <a href="https://www.mapbox.com/">Mapbox</a>',
    maxZoom: 18,
    id: 'mapbox/streets-v11',
    tileSize: 512,
    zoomOffset: -1,
    accessToken: 'pk.eyJ1IjoidGFyaWt1bDE1IiwiYSI6ImNrdDY3aWs4eTBmb3Eybm11aDFkOWRtY2wifQ.3xMdV8OfOGKDOxtrZ93gWw'
}).addTo(CurrentMap);
        leaflet.marker([51.5, -0.09]).addTo(CurrentMap);

      })
      const getIpinfo= async ()=>{
        try {
const data = await axios.get(`https://geo.ipify.org/api/v1?apiKey=at_aVJ4ZqcIvpD1BMxu8ZUhgxAhGJJSh&ipAddress=${queryIP.value}`)

const result = data.data
 
 IpInfo.value={
   address: result.ip,
   state: result.location.region,
   timezone: result.location.timezone,
   isp: result.isp,
   lat: result.location.lat,
   lng: result.location.lng,

 };
  leaflet.marker([IpInfo.value.lat,IpInfo.value.lng]).addTo(CurrentMap);
   leaflet.circle([51.508, -0.11], {
    color: 'red',
    fillColor: '#f03',
    fillOpacity: 0.5,
    radius: 500
}).addTo(CurrentMap);
  CurrentMap.setView([IpInfo.value.lat,IpInfo.value.lng], 13);

        }
        catch (err) {
alert(err.message);
        }
      }
      return {queryIP,IpInfo,getIpinfo};
    }
  }
</script>