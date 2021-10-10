<template>
  <Header />
  <transition name="fade">
    <router-view/>
  </transition>
</template>


<script>
import Header from '@/components/Header.vue'

export default {
  name: 'App',
  components: {
    Header
  }
}
</script>


<style lang="scss">
@import "./assets/vars.scss";

  body {
    background-color: $color-grey;
    min-height: 100vh;
    display: flex;
  }

  #app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;
    text-align: center;
    color: #2c3e50;
    flex: 1;
  }

  .fade-enter-active,
  .fade-leave-active {
    transition: opacity 0.5s ease;
  }


  .fade-enter-from,
  .fade-leave-to {
    opacity: 0;
  }
</style>
