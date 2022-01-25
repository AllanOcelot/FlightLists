<template>
  <div class="transitional-ref">
    <Preloader :loading="loading" :error="loadingError" />

    <div class="checklist-main">
      <div class="d-flex content-container" v-if="checklistData.Data">
        <div class="section-container">
          <h3>{{ checklistData.Name }}:</h3>
          <small>Author: {{ checklistData.Author }}, Last edit: {{ checklistData.Date }}</small>
          <hr />
          <p v-if="checklistData.Desc">{{ checklistData.Desc }}</p>
          <hr />
          <ul>
            <li
              v-for="section in checklistData.Data"
              :key="section.Title"
              @click="selected = section.Title"
              :class="{ active: selected === section.Title }"
            >
              <span class="visible-icon" v-if="selected === section.Title">
                <i class="bi bi-eye-fill"></i>
              </span>
              <span class="visible-icon" v-else>
                <i class="bi bi-eye-slash-fill"></i>
              </span>
              {{ section.Title }}
              <span
                class="section-progress"
              >{{ section.Completed }}/{{ section.Data.length }}</span>
            </li>
          </ul>
        </div>
        <div class="flex-fill checklist">
          <div
            class="checklist-parent"
            v-for="section in selectedChecklist.Data"
            :key="section.Title"
          >
            <div class="title" @click="section.Hidden = !section.Hidden">
              <span>{{ section.Title }} :</span>
              <span class="checklist-toggle">
                <i class="bi bi-arrows-expand" v-if="section.Hidden"></i>
                <i class="bi bi-arrows-collapse" v-else></i>
              </span>
              <span
                class="progress-bar"
                v-if="section.Progress"
                :style="{ 'width': section.Progress + '%' }"
                :class="{ complete: section.Progress === 100 }"
              ></span>
            </div>

            <div class="checklist-section" :class="{ visible: !section.Hidden }">
              <transition-group name="slide-fade">
                <div v-if="!section.Hidden">
                  <div
                    class="checklist-items"
                    v-for="checkItem in section.Data"
                    :key="checkItem.Name"
                  >
                    <div class="form-check">
                      <label class="form-check-label" :for="checkItem.Name">
                        <span>{{ checkItem.Name }}</span>
                        <br />
                        <small v-if="checkItem.Desc.length > 1">{{ checkItem.Desc }}</small>
                        <small v-else>&nbsp;</small>
                      </label>
                      <input
                        class="form-check-input lrg"
                        type="checkbox"
                        :id="checkItem.Name"
                        v-model="checkItem.Value"
                        v-on:change="updateProgress(section)"
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
import { defineComponent, ref, onMounted, watch } from "vue"

export default defineComponent({
  components: {
    Preloader
  },
  setup() {
    let loading = ref(true)
    let loadingError = ref(false)

    const route = useRoute()
    const dataID = route.params.id

    //What 'stage' is selected, i.e 'engine warm up' etc
    let selected = ref('Preflight')
    let selectedChecklist = ref({})

    let checklistData = ref({
      ID: 0,
      Name: '',
      Desc: '',
      Author: '',
      Date: '01/01/01',
      Progress: 0,
      Data: [{
        Title: '',
        Completed: 0,
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
          checklistData.value.Data[0].Data[0].Hidden = false
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

    // Watch the selection for change, as we need to display a difference Checklist 
    watch(selected, (newVal, prevVal) => {
      if (newVal != prevVal) {
        selected.value = newVal
        selectedChecklist.value = getMatchingChecklist()
      }
    })

    let getMatchingChecklist = function () {
      let checkList = checklistData.value.Data.filter((obj: any) => {
        return obj.Title === selected.value
      })
      return checkList[0];
    }

    return {
      loading,
      loadingError,
      selected,
      selectedChecklist,
      checklistData
    }
  },
  methods: {
    getPercentage(partialValue: number, totalValue: number) {
      return (100 * partialValue) / totalValue;
    },

    updateProgress(section: any) {
      let mainChecklistData: any = this.selectedChecklist;

      // Find the index of the section the checked item resides in.
      let sectionIndex = mainChecklistData.Data.findIndex((obj: any) => obj.Title === section.Title
      );

      // How many checkboxes are there, in this section.
      const lengthOfCheckItems = section.Data.length;

      // Query our check items, inside the section, to see how many have been completed 
      let checkItemsComplete = section.Data.filter((obj: any) => {
        return obj.Value === true
      })

      // Get a % based on this
      let percentComplete = this.getPercentage(checkItemsComplete.length, lengthOfCheckItems)
      section.Progress = percentComplete;

      if (percentComplete === 100) {
        // Check how many 'sections' have been completed.
        let sectionsComplete = mainChecklistData.Data.filter((obj: any) => {
          return obj.Progress === 100
        })
        mainChecklistData.Completed = sectionsComplete.length
        mainChecklistData.Data[sectionIndex].Hidden = true
        // check if the next index exists & is hidden, if it is, show it
        if (mainChecklistData.Data[sectionIndex + 1] && mainChecklistData.Data[sectionIndex + 1].Hidden === true) {
          mainChecklistData.Data[sectionIndex + 1].Hidden = false;
        }
      }
    },
  }
})
</script>

<style lang="scss" scoped>
@import "../assets/vars";
@import "../assets/vars";
@import "../assets/vars.scss";
@import "../assets/common.scss";

.transitional-ref {
  display: flex;
  flex: 1;
  flex-direction: column;
}

.checklist-main {
  background: #fff;
  overflow: hidden;
  position: relative;
  z-index: 0;
  flex: 1;
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

    ul {
      list-style: none;
      padding: 0;
      li {
        width: 100%;
        padding: 15px 10px;
        color: $color-white;
        background: $brand-blue-dark;
        font-size: 1.2rem;
        border-bottom: 2px solid $color-white;
        cursor: pointer;
        opacity: 0.85;
        transition: all 0.3s;

        &:hover {
          opacity: 1;
          transition: all 0.2s;
        }

        &.active {
          opacity: 1;
          background: $brand-green;
        }

        .visible-icon {
          float: left;
          width: 35px;
          text-align: center;
          margin-right: 10px;
        }

        .section-progress {
          float: right;
          font-size: 0.9rem;
          line-height: 30px;
        }

        p {
          &.active {
            background: $brand-blue;
            color: $color-white;
          }
        }
      }
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

.slide-fade-move {
  transition: transform 0.8s ease;
}

.slide-fade-enter-from,
.slide-fade-leave-to {
  transform: translateY(-50%);
  opacity: 0;
  transition: all 0.4s;
}
</style>