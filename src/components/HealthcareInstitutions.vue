<template>
  <div>
    <Card title="Screening Centres"> </Card>
    <dropdown v-model="region" :options="filteredRegions" displaykey="name" valuekey="name" unselected="Select Region" class="mt-3 mb-3"/>
    
    <div v-if="filteredLocations.length > 0">
      <div v-for="location in filteredLocations" :key="location.name" class="bg-gray-100 p-3 text-sm block">
      <p class="leading-normal font-bold capitalize"><i class="fas fa-map-marker-alt"></i> {{location.name}}</p>
      <p class="text-xs">{{location.address}}</p>
      <p class="text-right font-semibold"><i class="fas fa-phone"></i> {{location.telNo}}</p>
    </div>
    </div>
    <div v-else-if="country === 'global' || locations.some(loc => loc.country.toUpperCase() === country.toUpperCase())" class="bg-gray-100 p-3 text-sm block justify-center">
      <p class="text-4xl text-center"><i class="far fa-hand-point-up"></i></p>
      <p class="font-bold text-center capitalize">Select a region</p>
    </div>
    <div v-else-if="!(country === '' || country === 'global')" class="bg-gray-100 p-3 text-sm block justify-center">
      <p class="text-4xl text-center"><i class="far fa-frown"></i></p>
      <p class="font-bold text-center capitalize">Sorry!</p>
      <p class="text-center">We do not have any screening centre location data for the selected country or region yet!</p>
      <p class="text-center mt-2">Please select another country or region</p>
    </div>
    <div class="bg-gray-300 p-3 text-sm block justify-center">
      <p class="text-center mt-2">Help us collate the locations for your country!</p>
      <p class="font-bold text-center text-primary text-lg">
        <a target="_blank" href="https://t.me/joinchat/Jc3F5hR2yrcJ6OYlN9kXgw?fbclid=IwAR1oBafFFEo7HAnoUSX1T9nzdtFroXLtTUxn67yACnRRLrT2o-syWBZG_vI">
          <i class="fab fa-telegram"></i> Join our telegram group
        </a>
      </p>
    </div>
    
  </div>
</template>

<script>
import Card from "../components/Card";
import Dropdown from "../components/Dropdown";
import {getHealthcareInstitutions} from "../api/healthcareinstitutions";

export default {
  name: "HealthcareInstituitions",
  components: {
    Card,
    Dropdown
  },
  props:{
    country: {
      type:String,
      default:""
    }
  },
  data() {
    return {
      region: "",
      locations: [],
    };
  },
  computed:{
    filteredRegions: function(){
      console.dir(this.country);
      if (this.country === "" || this.country === "global")
        return this.regions;
      else
        return this.regions.filter(region => region.country.toUpperCase() === this.country.toUpperCase());
    },
    filteredLocations: function () {
      return this.locations.filter(loc => loc.state === this.region);
    },
    regions: function(){
      var regionsarray = [];

      this.locations.forEach(location => {
        var region = {name: location.state.toUpperCase(), country: location.country.toUpperCase()}
        
        if(!regionsarray.some(reg => reg.name === region.name)){
          regionsarray.push(region);
        }
      });

      return regionsarray;
    }
  },
  watch: {
    country() {
      // Reset region when country changes
      this.region = '';
    },
  },
  created() {
    getHealthcareInstitutions().then(data => {
      this.locations = data.hospitalsAndHealthcareProviders;
    })
  }
}
</script>

<style scoped>
</style>
