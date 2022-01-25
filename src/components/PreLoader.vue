<template>
  <div class="d-flex justify-content-center pre-loader" v-if="loading || error">
    <div class="spinner-border" role="status" v-if="loading && !error">
      <span class="sr-only"></span>
    </div>
    <p v-if="loading && !error">Now Loading, please wait...</p>
    <p v-else>There was an error loading this data</p>
    <router-link to="/">Return Home</router-link>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  props: {
    loading: Boolean,
    error: Boolean
  },
})
</script>

<style lang="scss" scoped>
</style>