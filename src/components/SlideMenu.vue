<template>
    <div>
        <Slide>
            <h3>Stuff</h3>
            <li>Links</li>
            <li>Info</li>
            <li>Links</li>
            <li>Info</li>
            <li>Links</li>
            <li>Info</li>
            <li>Links</li>
            <li>Info</li>
            <li><router-link v-bind:to="{ name: 'about' }"> About </router-link></li>
        </Slide>
    </div>
</template>

<script>
import { Slide } from 'vue3-burger-menu'
export default {
    components: {
        Slide
    }
}
</script>

<style></style>