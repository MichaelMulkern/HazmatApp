<template>
  <div class="home">
    <h1>TOP LOGO HERE</h1>
    <h2>The following app is intended to provide guidance on the classification for 
lithium ion and lithium metal batteries.</h2>

    <air-transport />
    <slide-menu />
    
  </div>
</template>

<script>
// @ is an alias to /src
import SlideMenu from '@/components/SlideMenu.vue'
import AirTransport from '@/components/AirTransport.vue'



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
