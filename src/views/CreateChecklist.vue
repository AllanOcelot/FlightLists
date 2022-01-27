<template>
  <div class="transitional-ref">
    <!-- 
    <Preloader :loading="loading" :error="loadingError" />
    -->

    <div class="checklist-main">
      <div class="d-flex content-container">
        <div class="section-container">
          <div class="title-container">
            <h3 v-if="!titleEdit">{{ checklist.Name }}</h3>
            <div class="form-group" v-else>
              <input
                type="text"
                class="form-control title-edit"
                id="title-edit"
                v-model="checklist.Name"
              />
            </div>
            <span class="edit" @click="titleEdit = !titleEdit">
              <i class="bi bi-pencil"></i>
            </span>
          </div>
          <hr />
          <div class="desc-container">
            <textarea
              name="description"
              id="description"
              rows="3"
              v-model="checklist.Desc"
              v-if="descEdit"
            ></textarea>
            <p v-else>{{ checklist.Desc }}</p>
            <span class="edit" @click="descEdit = !descEdit">
              <i class="bi bi-pencil"></i>
            </span>
          </div>
          <hr />
          <ul>
            <li v-for="list in checklist.Data" :key="list.Title">
              <span class="visible-icon" v-if="selected === list.Title">
                <i class="bi bi-eye-fill"></i>
              </span>
              <span class="visible-icon" v-else>
                <i class="bi bi-eye-slash-fill"></i>
              </span>
              {{ list.Title }}
            </li>
          </ul>

          <button @click="addList()">Add List</button>

          <!-- Submit the checklist / export? How do we save the data?-->
        </div>

        <div class="flex-fill checklist">
          <div
            class="checklist-parent"
            v-for="section in checklist.Data[getSelectedListIndex()].Data"
            :key="section.Title"
          >
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

              <button @click="addItem(checklist, section)">Add new item</button>
            </div>
          </div>

          <button @click="addSection()">Add New Section</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent, ref, reactive } from "vue"

export default defineComponent({
  name: 'Create',
  setup() {
    let titleEdit = ref(false)
    let descEdit = ref(false)
    let selected = ref("")

    const emptyList = {
      "Title": "Example",
      "Hidden": false,
      "Completed": 0,
      "Data": []
    }

    const emptySection = {
      "Title": "Cabin",
      "Hidden": true,
      "Data": []
    }


    const emptyItem = {
      "Name": "Item Title",
      "Type": "",
      "Value": false,
      "ToDo": "",
      "Desc": ""
    }

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
    let checklist = reactive({
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
        }
      ]
    }
    )

    function getSelectedListIndex() {
      const index = checklist.Data.findIndex(list => list.Title === selected.value)
      console.log("index is " + index)
      return index
    }




    // Set selected list to be the first list's title.
    selected.value = checklist.Data[0].Title
    getSelectedListIndex()

    console.log(checklist)

    return {
      titleEdit,
      descEdit,
      emptyList,
      emptySection,
      emptyItem,
      selected,
      checklist,
      getSelectedListIndex
    }

  },
  methods: {
    addList() {
      this.checklist.Data.push(this.emptyList)
    },

    addSection() {
      this.checklist.Data[this.getSelectedListIndex()].Data.push(this.emptySection)
    },

    addItem(checklist: any, section: any) {
      console.log(section)
      section.Data.push(this.emptyItem)
      console.log(checklist)
      console.log(section)
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

.section-container {
  .title-container,
  .desc-container {
    width: 100%;
    position: relative;

    .edit {
      position: absolute;
      z-index: 1;
      opacity: 0.8;
      cursor: pointer;
      right: 10px;
      top: 5px;
      &:hover {
        opacity: 1;
      }
    }

    textarea,
    .title-edit {
      width: 100%;
      padding: 5px 35px 5px 5px;
    }
  }
  .desc-container .edit {
    top: 0;
  }
}

.checklist-main .checklist .checklist-parent .title {
  cursor: default;
}

.checklist-items {
  text-align: left;
  .form-group.row {
    margin-bottom: 1rem;
  }
}
</style>