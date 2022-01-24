<template>
  <div
    class="d-flex justify-content-center pre-loader"
    v-if="loading === true || ListData.length == 0"
  >
    <div class="spinner-border" role="status" v-if="loading">
      <span class="sr-only"></span>
    </div>
    <p v-if="loading">Now Loading, please wait...</p>
    <p v-else>There was an error loading this data</p>
    <router-link to="/">Return Home</router-link>
  </div>

  <div class="introduction-banner left">
    <h1>List of Aircraft:</h1>
    <p>
      Below you can find the list of Aircraft we have checklists for and those we are working on!
      <br />You can always
      <router-link to="contribute">contribute</router-link>&nbsp; your own checklists to FlightLists!
    </p>
  </div>

  <div class="list-container" v-if="!loading && ListData.length > 0">
    <div class="container">
      <div class="row">
        <div class="col">
          <div class="content-wrapper">
            <table class="table table-striped table-hover">
              <thead>
                <tr>
                  <th scope="col">#</th>
                  <th scope="col">Aircraft Name:</th>
                  <th scope="col">Status:</th>
                  <th></th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="item in ListData" :key="item.ID" @click="viewList(item.Filename)">
                  <th scope="row" class="align-middle">{{ item.ID }}</th>
                  <td class="align-middle">{{ item.Name }}</td>
                  <td class="align-middle">{{ item.Status }}</td>
                  <td class="align-middle">
                    <router-link
                      class="btn btn-outline-secondary"
                      :to="'/Checklist/' + item.Filename"
                    >View</router-link>
                  </td>
                </tr>
              </tbody>
            </table>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script type="ts">
import axios from "axios";
import { defineComponent } from "vue";

export default defineComponent({
  name: 'List',
  data: function () {
    return {
      loading: true,
      loadingError: false,
      ListData: []
    }
  },
  mounted() {
    // Request the Data.
    axios
      .get("/data/list.json")
      .then(response => {
        this.loading = false
        this.ListData = response.data
      }).catch(error => {
        this.loading = false
        console.log('Error with loading data is: ' + error)
      })
  }
});
</script>

<style lang="scss" scoped>
@import "../assets/vars.scss";
@import "../assets/common.scss";

.list-container {
  .info {
    background: #fff;
    text-align: left;
  }

  table {
    text-align: left;
  }
}
</style>
