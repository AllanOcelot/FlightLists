<template>
  <Preloader :loading="loading" :error="loadingError" />

  <div class="checklist-main">
    <div class="d-flex content-container" v-if="checklistData.Data">
      <div class="section-container">
        <h3>{{ checklistData.Name }}:</h3>
        <small>Author: {{ checklistData.Author }}, Last edit: {{ checklistData.Date }}</small>
        <hr />
        <h3>Sections:</h3>
        <div class="section" v-for="item in checklistData.Data" :key="item.Title">
          <p>{{ item.Title }}</p>
          <ul>
            <li v-for="checklist in item.Data" :key="checklist.Title">{{ checklist.Title }}</li>
          </ul>
        </div>
      </div>

      <div
        class="flex-fill checklist"
        v-for="checklist in checklistData.Data"
        :key="checklist.Title"
      >
        <div v-if="selected === checklist.Title">
          <div class="checklist-parent" v-for="item in checklist.Data" :key="item.Title">
            <div class="title" @click="item.Hidden = !item.Hidden">
              <span>{{ item.Title }} :</span>
              <span class="checklist-toggle">
                <i class="bi bi-arrows-expand" v-if="item.Hidden"></i>
                <i class="bi bi-arrows-collapse" v-else></i>
              </span>
              <span
                class="progress-bar"
                :style="{ 'width': item.Progress + '%' }"
                :class="{ complete: item.Progress === 100 }"
              ></span>
            </div>

            <div class="checklist-section" :class="{ visible: !item.Hidden }">
              <transition-group name="slide-fade">
                <div v-if="!item.Hidden">
                  <div
                    class="checklist-items"
                    v-for="checklistItem in item.Data"
                    :key="checklistItem.Name"
                  >
                    <div class="form-check">
                      <label class="form-check-label" :for="checklistItem.Name">
                        <span>{{ checklistItem.Name }}</span>
                        <br />
                        <small v-if="checklistItem.Desc.length > 1">{{ checklistItem.Desc }}</small>
                        <small v-else>&nbsp;</small>
                      </label>
                      <input
                        class="form-check-input lrg"
                        type="checkbox"
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
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import Preloader from "@/components/PreLoader.vue"
import axios from "axios"
import { useRoute } from 'vue-router'
import { defineComponent, ref, onMounted } from "vue"

export default defineComponent({
  components: {
    Preloader
  },
  setup() {
    let loading = ref(true)
    let loadingError = ref(false)

    const route = useRoute()
    const dataID = route.params.id

    let selected = ref('Preflight')
    let checklistData = ref({
      ID: 0,
      Name: '',
      Desc: '',
      Author: '',
      Date: '01/01/01',
      Progress: 0,
      Data: [{
        Title: '',
        Progress: 0,
        Data: [{
          Title: '',
          Hidden: false,
          Progress: 0,
          Data: [{
            Name: '',
            Type: '',
            Value: false,
            ToDo: '',
            Desc: ''
          }]
        }]
      }]
    })


    // Request the Data.
    const getChecklist = async () => {
      try {
        const response = await axios.get("/data/" + dataID + ".json")
        checklistData.value = response.data
        if (checklistData.value.Data[0]) {
          selected.value = checklistData.value.Data[0].Title
          //checklistData.value.Data[0].Data[0].Hidden = false
        }
      } catch (err) {
        console.log('There was an error fetching the Checklist with ID ' + dataID + ' | Error : ' + err)
        selected.value = 'error'
        loadingError.value = true
      } finally {
        loading.value = false
      }
    }

    onMounted(getChecklist)


    return {
      loading,
      loadingError,
      selected,
      checklistData
    }
  },
  methods: {
    getPercentage(partialValue: number, totalValue: number) {
      return (100 * partialValue) / totalValue;
    },

    // Update the progress of the current checklist here.
    updateProgress(Checklist: any, Parent: any) {
      // Ensure we are working on the correct Checklist.
      let ChecklistData = this.checklistData.Data.filter(obj => {
        return obj.Title === Checklist.Title
      })

      // Ensure we are working on the right 'section' of the current Playlist.
      let ChecklistChildData = ChecklistData[0].Data.filter(obj => {
        return obj.Title === Parent.Title
      })
      // We grab it's index too, for future use.
      let ChecklistChildIndex = ChecklistData[0].Data.findIndex(obj => obj.Title === Parent.Title
      );

      // How many checkboxes are there, in this child checklist.
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
    }
  }
})
</script>

<style lang="scss" scoped>
@import "../assets/vars";
@import "../assets/vars";
@import "../assets/vars.scss";
@import "../assets/common.scss";

.checklist-main {
  background: #fff;
  margin-bottom: 50px;
  overflow: hidden;
  position: relative;
  z-index: 0;
  min-height: calc(100% - 58px);
  display: flex;
  flex-direction: column;

  .content-container {
    flex: 1;
  }

  .info {
    padding: 60px 0 40px 0;
    background: $color-grey;
    color: $color-text-dark;

    h3 {
      margin: 0;
    }
    p {
      padding: 0;
      margin: 0;
    }
  }

  .section-container {
    text-align: left;
    width: 25%;
    padding: 20px;
    h3 {
      margin: 0;
    }
  }

  .checklist {
    background: $color-grey;
    .checklist-parent {
      background: $color-grey;
      border-bottom: 1px solid #d9d9d9;
      overflow: hidden;
      .title {
        font-size: 1.6em;
        text-align: left;
        padding: 10px 20px;
        margin: 0;
        background: $brand-blue;
        font-weight: 600;
        color: #fff;
        position: relative;
        z-index: 10;
        cursor: pointer;

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
        transition: all 0.6s ease-in-out;

        &.visible {
          opacity: 1;
          padding: 20px 20px 0 20px;
        }

        .checklist-items {
          padding-bottom: 20px;
          .form-check {
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
              margin-left: auto;
              width: 34px;
              height: 34px;
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
      background-color: $brand-green;
      border-color: $brand-green;
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
    background: $brand-green;
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

.slide-fade-enter-from,
.slide-fade-leave-to {
  transform: translateY(-50%);
  opacity: 0;
  transition: all 0.4s;
}
</style>