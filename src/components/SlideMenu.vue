<template>
    <div>
        <Slide>
            <a id="home" href="#">
                <span>Home</span>
            </a>
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