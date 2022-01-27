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
@import "../assets/vars.scss";
@import "../assets/common.scss";
@import "../assets/checklist.scss";
</style>