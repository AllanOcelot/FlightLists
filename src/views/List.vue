<template>
  <Preloader :loading="loading" :error="loadingError" />

  <div class="introduction-banner overlay text-left margin-none">
    <h1>List of Aircraft:</h1>
    <p>
      Below you can find the list of Aircraft we have checklists for and those we are working on!
      <br />If you would like to, you can
      <router-link to="contribute">contribute a checklist</router-link>.
    </p>
  </div>

  <div class="list-container" v-if="!loading && listData.length > 0">
    <div class="content-wrapper">
      <table class="table table-striped table-hover">
        <thead>
          <tr>
            <th scope="col first">#</th>
            <th scope="col">Aircraft Name:</th>
            <th scope="col">Status:</th>
            <th></th>
          </tr>
        </thead>
        <tbody>
          <tr v-for="item in listData" :key="item.ID">
            <td class="align-middle first">{{ item.ID }}</td>
            <td class="align-middle">{{ item.Name }}</td>
            <td class="align-middle">{{ item.Status }}</td>
            <td class="align-middle last action-container">
              <router-link :to="'/Checklist/' + item.Filename">
                <button class="btn">View</button>
              </router-link>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </div>
</template>

<script type="ts">
import Preloader from '@/components/PreLoader.vue'
import axios from "axios";
import { defineComponent, onMounted, ref } from "vue";

export default defineComponent({
  name: 'List',
  components: {
    Preloader
  },
  setup() {
    let loading = ref(true)
    let loadingError = ref(false)
    let listData = ref([])

    const fetchList = async () => {
      try {
        const response = await axios.get("/data/list.json")
        listData.value = response.data
      } catch (err) {
        console.log(err)
        loadingError = true
      } finally {
        loading.value = false
      }
    }

    onMounted(fetchList)

    return {
      loading,
      loadingError,
      listData
    }

  },
});
</script>

<style lang="scss" scoped>
@import "../assets/vars.scss";
@import "../assets/common.scss";

.introduction-banner {
  background-image: url("/assets/images/bg2.png");
}

.list-container {
  display: flex;
  flex: 1;
  flex-direction: column;
  .info {
    background: #fff;
    text-align: left;
  }

  table {
    text-align: left;
    th:nth-child(1),
    td.first {
      padding-left: 20px;
    }
    td.last {
      padding-right: 20px;
    }
    .action-container {
      text-align: right;
    }
  }
}
</style>
