<template>
  <div
    class="d-flex justify-content-center pre-loader"
    v-if="loading === true || loadingError === true"
  >
    <div class="spinner-border" role="status" v-if="!loadingError">
      <span class="sr-only"></span>
    </div>
    <p v-if="!loadingError">Now Loading, please wait...</p>
    <p v-else>There was an error loading this data</p>
    <router-link to="/">Return Home</router-link>
  </div>

  <div class="checklist-main">
    <div class="info" v-if="CheckListData.Name">
      <h3>{{ CheckListData.Name }}:</h3>
      <p>{{ CheckListData.Desc }}</p>
    </div>

    <div class="checklist-container" v-if="CheckListData.Data">
      <div class="d-flex">
        <div class="button-container">
          <p>Section:</p>
          <ul class="selection" v-for="item in CheckListData.Data" :key="item">
            <li :class="{ 'active': selected === item.Title }" @click="selected = item.Title">
              >
              {{ item.Title }}
            </li>
          </ul>
        </div>

        <div
          class="flex-fill checklist"
          v-for="checklist in CheckListData.Data"
          :key="checklist.Title"
        >
          <div v-if="selected === checklist.Title">
            <div class="checklist-parent" v-for="item in checklist.Data" :key="item">
              <div class="title">
                <span>{{ item.Title }} :</span>
                <div
                  class="progress-bar"
                  :style="{ 'width': item.Progress + '%' }"
                  :class="{ complete: item.Progress === 100 }"
                ></div>
                <span class="checklist-toggle" @click="item.Hidden = !item.Hidden">
                  <i class="bi bi-arrows-expand" v-if="item.Hidden"></i>
                  <i class="bi bi-arrows-collapse" v-else></i>
                </span>
              </div>

              <div class="checklist-section" :class="{ visible: !item.Hidden }">
                <transition-group name="slide-fade">
                  <div v-if="!item.Hidden">
                    <div
                      class="checklist-items"
                      v-for="checklistItem in item.Data"
                      :key="checklistItem.Name"
                    >
                      <div class="form-check form-switch">
                        <label class="form-check-label" :for="checklistItem.Name">
                          <span>{{ checklistItem.Name }}</span>
                          <br />
                          <small v-if="checklistItem.Desc.length > 1">{{ checklistItem.Desc }}</small>
                          <small v-else>&nbsp;</small>
                        </label>
                        <input
                          class="form-check-input"
                          type="checkbox"
                          role="switch"
                          :id="checklistItem.Name"
                          v-model="checklistItem.Value"
                          v-on:change="updateProgress(checklist, item)"
                        />
                      </div>
                    </div>
                  </div>
                </transition-group>
              </div>
            </div>

            <div class="checklist-progress">
              <p class="title">Progress:</p>
              <span class="progress-bar"></span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import axios from "axios";
import { useRoute } from 'vue-router';

export default {
  name: 'CheckList',
  props: {
    id: Number
  },
  data: function () {
    return {
      loading: true,
      loadingError: false,
      selected: 'Preflight',
      CheckListData: {}
    }
  },
  mounted() {
    // Get the ID from our Route
    const route = useRoute();

    console.log('--------------')
    console.log(route.params.id)
    console.log('--------------')

    const dataID = route.params.id;

    // Request the Data.
    axios
      .get("/data/" + dataID + ".json")
      .then(response => {
        this.loading = false
        this.CheckListData = response.data

        console.log(this.CheckListData.Data[0])
        if (this.CheckListData.Data[0]) {
          this.selected = this.CheckListData.Data[0].Title
          this.CheckListData.Data[0].Data[0].Hidden = false
        }

      }).catch(error => {
        console.log('Error in console ' + error)
        this.loadingError = true
      })
  },
  methods: {
    getPercentage(partialValue, totalValue) {
      return (100 * partialValue) / totalValue;
    },

    // Update the progress of the current checklist here.
    updateProgress(Checklist, Parent) {

      // Ensure we are working on the correct Checklist.
      let ChecklistData = this.CheckListData.Data.filter(obj => {
        return obj.Title === Checklist.Title
      })

      // Ensure we are working on the right 'section' of the current Playlist.
      let ChecklistChildData = ChecklistData[0].Data.filter(obj => {
        return obj.Title === Parent.Title
      })
      // We grab it's index too, for future use.
      let ChecklistChildIndex = ChecklistData[0].Data.findIndex(obj => obj.Title === Parent.Title
      );

      // How many checkboxes are there, in this checklist section?
      const lengthOfCheckItems = ChecklistChildData[0].Data.length;

      // Finally, we can query our ChecklistChildData, to see how many have been completed 
      let ChecklistChildCompleted = ChecklistChildData[0].Data.filter(obj => {
        return obj.Value === true
      })

      // Update our values. 
      let PercentComplete = this.getPercentage(ChecklistChildCompleted.length, lengthOfCheckItems);
      Parent.Progress = PercentComplete;
      if (PercentComplete === 100) {
        Parent.Hidden = true;
        // check if the next index exists & is hidden, if it is, show it
        if (ChecklistData[0].Data[ChecklistChildIndex + 1] && ChecklistData[0].Data[ChecklistChildIndex + 1].Hidden === true) {
          ChecklistData[0].Data[ChecklistChildIndex + 1].Hidden = false;
        }
      }

      // TODO, 
      // Maybe track how far through the entire checklist the user is. This might be overkill.


    }

  }
}
</script>

<style lang="scss" scoped>
@import "../assets/vars";
@import "../assets/vars.scss";
@import "../assets/common.scss";

.checklist-main {
  background: #fff;
  margin-bottom: 50px;
  overflow: hidden;

  .selection-container {
  }

  .info {
    padding: 60px 0 40px 0;
    background: $brand-blue;
    color: $color-white;

    h3 {
      margin: 0;
    }
    p {
      padding: 0;
      margin: 0;
    }
  }

  .checklist {
    .checklist-parent {
      background: $color-grey;
      border-bottom: 1px solid #d9d9d9;
      overflow: hidden;
      .title {
        font-size: 1.6em;
        text-align: left;
        padding: 10px 20px;
        margin: 0;
        background: $color-steel;
        font-weight: 600;
        color: #fff;
        position: relative;
        z-index: 10;

        span {
          z-index: 1;
          position: relative;
        }

        .checklist-toggle {
          position: absolute;
          z-index: 100;
          right: 20px;
          font-size: 18px;
          border: 1px solid #fff;
          width: 34px;
          height: 34px;
          top: calc(50% - 16px);
          line-height: 31px;
          text-align: center;
          border-radius: 8px;
          opacity: 0.7;
          transition: opacity 0.3s;
          cursor: pointer;
          &:hover {
            opacity: 1;
          }
        }

        .progress-bar {
          z-index: 0;
          position: absolute;
          left: 0;
          width: 0;
          top: 0;
          height: 100%;
          background: $brand-green;
          transition: all 0.4s;
          transition-delay: all 0.3s;
          pointer-events: none;
          &.complete {
            transition: width 0.4s, background 0.4s;
          }
        }
      }

      .checklist-section {
        position: relative;
        z-index: 0;
        opacity: 0;
        background: $color-white;
        transition: all 0.6s ease-in-out;

        &.visible {
          opacity: 1;
          padding: 20px 20px 0 20px;
        }

        .checklist-items {
          padding-bottom: 20px;
          .form-switch {
            display: flex;
            align-items: center;
            padding: 0;

            label {
              pointer-events: none;
              width: 100%;
              text-align: left;
              span {
                font-size: 1.2em;
              }
            }

            input {
              float: right;
              cursor: pointer;
            }
          }
        }
      }

      &.error {
        .title {
          background: $title-bg-error;
        }
      }
      &.succ {
        .title {
          background: $title-bg-succ;
        }
      }
    }

    .form-check-input:checked {
      background-color: $brand-blue;
      border-color: $brand-blue;
    }
  }
}

.checklist-progress {
  padding-top: 25px;
  width: 100%;
  position: relative;
  p.title {
    font-size: 1em;
    padding: 0 20px;
    font-weight: 600;
    text-align: left;
  }
  .progress-bar {
    height: 10px;
    float: left;
    width: 0;
    background: green;
  }
}

// Transitions here.
.slide-fade-enter-active {
  transition: all 0.3s ease-out;
  transition-delay: 0.35s;
}

.slide-fade-leave-active {
  transition: all 0.4s cubic-bezier(1, 0.5, 0.8, 1);
}

.slide-fade-leave-to {
}

.slide-fade-enter-from,
.slide-fade-leave-to {
  transform: translateY(-50%);
  opacity: 0;
  transition: all 0.4s;
}
</style>