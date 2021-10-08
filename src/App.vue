<template>
  <Header />
  <router-view/>
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


<style>

body {
  background-image: url('assets/bg.jpg');
  background-size: cover;
  background-position:  right;
  background-repeat: no-repeat;
  background-color: #B6E3F8;
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

#nav {
  padding: 30px;
}

#nav a {
  font-weight: bold;
  color: #2c3e50;
}

#nav a.router-link-exact-active {
  color: #42b983;
}
</style>
