<template>
  <div class="home">
    <h1>TOP LOGO HERE</h1>
    <h2>The following app is intended to provide guidance on the classification for
      lithium ion and lithium metal batteries.</h2>
    <p id="top-paragraph">All cells and batteries must be tested in accordance
      with the UN Manual of Tests and Criteria Part III
      Subsection 38.3 (DGR 3.9.2.6)</p>
    <hr>

    <battery-tree />
    <slide-menu />

   
  </div>
</template>

<script>
// @ is an alias to /src
import SlideMenu from '@/components/SlideMenu.vue'
import BatteryTree from '@/components/BatteryTree.vue'




export default {
  name: 'HomeView',
  data() {
    return {
      airBool: false,
    }
  },
  components: {
    SlideMenu,
    BatteryTree,


  },
  methods: {
    showTransport() {
      switch (this.transport) {
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
