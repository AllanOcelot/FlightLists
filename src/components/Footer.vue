<template>
  <footer class="footer-main">
    <p>
      Created by:
      <a href="https://www.allancodes.com" alt="AllanCodes" target="_blank">AllanCodes</a>,
    </p>
    <p>
      Contribute at
      <a href="https://www.github.com/AllanOcelot/FlightLists">GitHub</a>,
    </p>
    <p>
      Please be sure to read our
      <router-link to="/content-policy">Content Policy</router-link>.
    </p>
  </footer>
</template>

<script>
export default {
  name: 'Footer',
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
@import "../assets/vars.scss";

.footer-main {
  border-top: 2px solid $brand-yellow;
  background: $color-white;
  padding: 10px 20px;
  text-align: right;
  font-size: 14px;
  p {
    margin: 0;
    display: inline-block;
    margin-right: 5px;
    a {
      color: $brand-blue;
      text-decoration: none;
    }
  }
}
</style>
