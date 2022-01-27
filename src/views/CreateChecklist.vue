<template>
  <div class="transitional-ref">
    <!-- 
    <Preloader :loading="loading" :error="loadingError" />
    -->

    <div class="checklist-main">
      <div class="d-flex content-container">
        <div class="section-container">
          {{ checklist }}
          <h3>{{ checklist.Name }}</h3>
          <textarea
            name="description"
            id="description"
            cols="30"
            rows="10"
            v-model="checklist.Desc"
          ></textarea>

          <!-- Submit the checklist / export? How do we save the data?-->
          <div></div>
        </div>

        <div class="flex-fill checklist">
          <div class="checklist-parent" v-for="section in checklist.Data" :key="section.Title">
            <div class="title">{{ section.Title }}</div>
            <div class="checklist-section visible">
              <div class="checklist-items" v-for="item in section.Data" :key="item.Name">
                <div class="form-group row">
                  <label for="input-title" class="col-sm-2 col-form-label">Item Title:</label>
                  <div class="col-sm-10">
                    <input
                      type="text"
                      class="form-control"
                      id="item-title"
                      :placeholder="item.Name"
                    />
                  </div>
                </div>
                <div class="form-group row">
                  <label for="input-title" class="col-sm-2 col-form-label">Item Details:</label>
                  <div class="col-sm-10">
                    <input
                      type="text"
                      class="form-control"
                      id="item-details"
                      placeholder="Please provide a title"
                    />
                  </div>
                </div>
              </div>

              <button>Add new item</button>
            </div>
          </div>

          <button>Add New Section</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref } from "vue"

export default defineComponent({
  name: 'Create',
  setup() {
    /*
    interface type_checklist {
      "ID": number,
      "Name": String,
      "Desc": String,
      "Author": String,
      "Date": String,
      "Data": [{

      }]
    }
    */


    // The 'Blank' Checklist all others are built from.
    let checklist = ref({
      "ID": 1,
      "Name": "Cessna 172",
      "Desc": "Dummy Content, for testing only",
      "Author": "AllanCodes",
      "Date": "24/01/22",
      "Data": [
        {
          "Title": "PreFlight",
          "Desc": "example text to go here, example text to go here, example text to go here",
          "Completed": 0,
          "Data": [
            {
              "Title": "Cabin",
              "Hidden": true,
              "Data": [
                {
                  "Name": "Documents",
                  "Type": "Text",
                  "Value": false,
                  "ToDo": "A.R.R.O.W",
                  "Desc": "Remember to check A.R.R.O.W"
                }
              ]
            }
          ]
        }]
    }
    )


    console.log(checklist)

    return {
      checklist
    }

  },
  components: {

  },
})
</script>

<style lang="scss" scoped>
@import "../assets/vars.scss";
@import "../assets/common.scss";
@import "../assets/checklist.scss";

.checklist-items {
  text-align: left;
  .form-group.row {
    margin-bottom: 1rem;
  }
}
</style>