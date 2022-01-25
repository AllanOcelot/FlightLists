<template>
  <nav class="navbar navbar-expand-lg navbar-light bg-light">
    <router-link to="/" class="navbar-brand">FlightLists</router-link>
    <button
      class="navbar-toggler"
      type="button"
      data-bs-toggle="collapse"
      data-bs-target="#navbarSupportedContent"
      aria-controls="navbarSupportedContent"
      aria-expanded="false"
      aria-label="Toggle navigation"
    >
      <span class="navbar-toggler-icon"></span>
    </button>
    <div class="collapse navbar-collapse" id="navbarSupportedContent">
      <ul class="navbar-nav me-auto mb-2 mb-lg-0">
        <li class="nav-item">
          <router-link to="/List" class="nav-link">View All</router-link>
        </li>
        <li class="nav-item dropdown">
          <a
            class="nav-link dropdown-toggle"
            href="#"
            id="navbarDropdown"
            role="button"
            data-bs-toggle="dropdown"
            aria-expanded="false"
          >More</a>
          <ul class="dropdown-menu" aria-labelledby="navbarDropdown">
            <li>
              <a class="dropdown-item" href="#">FAQ</a>
            </li>
            <li>
              <a class="dropdown-item" href="#">Contribute</a>
            </li>
            <li>
              <hr class="dropdown-divider" />
            </li>
            <li>
              <router-link to="/Create" class="dropdown-item">Create Checklist</router-link>
            </li>
          </ul>
        </li>
      </ul>
      <!--
            <form class="d-flex">
              <input class="form-control me-2" type="search" placeholder="Search" aria-label="Search">
              <button class="btn btn-outline-success" type="submit">Search</button>
            </form>
      -->
    </div>
  </nav>
</template>

<script>
export default {
  name: 'Header',
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style lang="scss" scoped>
@import "../assets/vars.scss";

.navbar {
  border-bottom: 2px solid $brand-yellow;
  padding-left: 20px;
  padding-right: 20px;

  .container {
    display: block;
    justify-content: initial;
  }

  .navbar-brand {
    float: left;
    color: $brand-blue;
    font-weight: 600;
    letter-spacing: 1px;
    border-right: 1px solid $color-grey;
    padding-right: 15px;
  }
}
</style>
