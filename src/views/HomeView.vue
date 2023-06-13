<template>
  <div class="home">
    <h1>HOME VIEW</h1>
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
  components: {
    SlideMenu,
    BatteryTree
  }
}
</script>
