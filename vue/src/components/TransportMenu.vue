<template>
  <div>
    <h2>TRANSPORT</h2>
    <h3>{{ zip }} here</h3>
  </div>
</template>

<script>
export default {
  data() {
    return {
      zip: "",
    }
  },
  created() {
    this.setThings();
  },
  methods: {
    setThings() {
      this.zip = this.$store.state.zap;
    },
  }
}


</script>

<style></style>