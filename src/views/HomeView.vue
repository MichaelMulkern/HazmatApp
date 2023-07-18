<template>
  <body>
    <div class="home">
      <img src="../assets/genericLogo3.jpg" alt="Company Logo" id="top-logo">
      <div class="perm-set">
        <button @click="reloadPage">Start Over</button>
      </div>
      <div class="intro-block" v-if="introBlock">
        <h3>The following app is intended to provide guidance on the classification for
          lithium ion and lithium metal batteries traveling to, from, or within the United States.</h3>
        <p id="top-paragraph">All cells and batteries must be tested in accordance
          with the UN Manual of Tests and Criteria Part III
          Subsection 38.3 (DGR 3.9.2.6)</p>
        <div class="button-wrapper">
          <button class="nav-buttons" v-on:click="startApp()">BEGIN</button>
        </div>

      </div>
      <battery-tree v-if="battTree" />

      <slide-menu />


    </div>
  </body>
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
      introBlock: true,
      battTree: false
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
    },
    reloadPage() {
      window.location.reload();
    },
    startApp(){
      this.introBlock = false;
      this.battTree = true;
    }
  }
}
</script>

<style scoped>
body {
  background-image: url("../assets/containerYard.jpeg");
  background-size: cover;
  width: 100vw;
  height: 100vh;
}

.home {
  background-color: rgba(56, 56, 56, 0.829);
  color: aliceblue;
  height: auto;
}

#top-logo {
  width: 20%;
  height: 20%;
  margin-top: 10px;
}

.intro-block {
  padding-bottom: 10px;
 
}

.perm-set {
  display: block;
  border-bottom: rgb(54, 54, 54) 3px solid;
  padding-bottom: 5px;
}

.nav-buttons {
  margin-left: 70px;
  margin-right: 70px;

}


.nav-buttons {
  background-image: linear-gradient(-180deg, #37AEE2 0%, #1E96C8 100%);
  border-radius: .5rem;
  box-sizing: border-box;
  color: #FFFFFF;
  font-size: 20px;
  font-weight: bold;
  padding: 0.5rem 0.75rem;
  text-decoration: none;
  width: auto;
  border: 0;
  cursor: pointer;
  user-select: none;
  -webkit-user-select: none;
  touch-action: manipulation;
}

.nav-buttons:hover {
  background-image: linear-gradient(-180deg, #1D95C9 0%, #17759C 100%);
}
</style>
