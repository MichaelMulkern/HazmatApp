<template>
  <div class="home">
    <h1>TOP LOGO HERE</h1>
    <h2>The following app is intended to provide guidance on the classification for 
lithium ion and lithium metal batteries.</h2>
    <span>Select mode of transport: </span>
    <select 
    v-on:change="showTransport()"
    name="transport-list"
    class="dropdown"
    v-model="transport">
    <option class="drop-option" value="air">Air</option>
    <option class="drop-option" value="ocean">Maritime</option>
    <option class="drop-option" value="ground">49 CFR Road/Rail</option>
  </select>

    <air-transport v-show="airBool"/>
    <ground-transport v-show="groundBool"/>
    <ocean-transport v-show="oceanBool"/>
    <slide-menu />
    
  </div>
</template>

<script>
// @ is an alias to /src
import SlideMenu from '@/components/SlideMenu.vue'
import AirTransport from '@/components/AirTransport.vue'
import GroundTransport from '@/components/GroundTransport.vue'
import OceanTransport from '@/components/OceanTransport.vue'


export default {
  name: 'HomeView',
  data() {
    return {
      transport: "",
      airBool: false,
      groundBool: false,
      oceanBool: false
    }
  },
  components: {
    SlideMenu,
    AirTransport,
    GroundTransport,
    OceanTransport
  
  },
  methods:{
  showTransport(){
    switch(this.transport){
      case "air":
        this.airBool = true
        this.groundBool = false
        this.oceanBool = false
        break;
      case "ground":
        this.airBool = false
        this.groundBool = true
        this.oceanBool = false
        break;
      case "ocean":
        this.airBool = false
        this.groundBool = false
        this.oceanBool = true
        break;
    }
  }
}
}
</script>
