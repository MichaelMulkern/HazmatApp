<template>
    <div id="main-menu">
        <Slide>
            <button @click="reloadPage">Start Over</button>
            <li><router-link v-bind:to="{ name: 'about' }"> About </router-link></li>
        </Slide>
    </div>
</template>

<script>
import { Slide } from 'vue3-burger-menu'
export default {
    components: {
        Slide
    },
    methods: {
    reloadPage() {
      window.location.reload();
    }
  }
}
</script>

<style>
#main-menu {
    background-color: aliceblue;
}
</style>