<template>
  <div class="introduction-banner height-full margin-none overlay">
    <h1>Welcome to FlightLists!</h1>
    <p>Flightlists is an open source, community generated, aircraft checklist tool for use in Flight Simulators.</p>
    <div class="actions">
      <router-link to="/list">
        <button type="button" class="btn btn-lg">View All</button>
      </router-link>
      <router-link to="/create">
        <button type="button" class="btn btn-lg">Create Checklist</button>
      </router-link>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Home',
};
</script>

<style lang="scss" scoped>
@import "../assets/vars.scss";
@import "../assets/common.scss";

.introduction-banner {
  background-image: url("/assets/images/bg1.png");
}
</style>
