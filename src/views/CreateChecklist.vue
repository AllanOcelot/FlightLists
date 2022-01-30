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
            <li
              v-for="(list, index) in checklist.Data"
              :class="{ active: selected === index }"
              :key="index"
              @click="selected = index"
            >
              <span class="visible-icon">
                <i v-if="selected === index && !list.Edit" class="bi bi-eye-fill"></i>
                <i v-if="selected !== index && !list.Edit" class="bi bi-eye-slash-fill"></i>
                <i v-if="list.Edit" class="bi bi-trash remove" @click="removeList(index)"></i>
              </span>
              <span v-if="!list.Edit">{{ list.Title }}</span>
              <input type="text" class="form-control" id="item-title" v-model="list.Title" v-else />
              <span class="edit-icons" @click="list.Edit = !list.Edit">
                <span class="edit">
                  <i class="bi bi-pencil" v-if="!list.Edit"></i>
                  <i class="bi bi-x close" v-else></i>
                </span>
              </span>
            </li>
          </ul>

          <button class="btn btn-outline" @click="addList()">Add New Checklist</button>

          <div class="actions"></div>

          <!-- Submit the checklist / export? How do we save the data?-->
          <!-- TODO : Let's see if we can somehow export the JSON data via the Github Pull Request API, so users can create a PR easily. -->
        </div>

        <div class="flex-fill checklist">
          <div
            class="checklist-parent"
            v-for="(section, index) in checklist.Data[getSelectedListIndex()].Data"
            :key="index"
          >
            <div class="title">
              <span v-if="!section.Edit">{{ section.Title }}</span>
              <div class="form-group" v-else>
                <input type="text" class="form-control title-edit" v-model="section.Title" />
              </div>
              <span class="edit" @click="section.Edit = !section.Edit">
                <span v-if="section.Edit">
                  <i class="bi bi-x close"></i>
                </span>
                <span v-else>
                  <i class="bi bi-pencil"></i>
                </span>
              </span>
            </div>
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
                      placeholder="Please provide details"
                    />
                  </div>
                </div>
              </div>

              <span
                class="btn btn-outline-dark add-item"
                @click="addItem(checklist, section)"
              >Add new item to section</span>
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
    let selected = ref(0)



    const emptySection = {
      "Title": "Cabin",
      "Hidden": true,
      "Edit": false,
      "Data": Array()
    }

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
          "Edit": false,
          "Data": [
            {
              "Title": "Cabin",
              "Hidden": true,
              "Edit": false,
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
      //const index = checklist.Data.findIndex(list => list.Title === selected.value)
      //console.log("index is " + index)
      const index = selected.value
      return index
    }

    // Set selected list to be the first list's title.
    selected.value = 0
    getSelectedListIndex()

    console.log(checklist)

    return {
      titleEdit,
      descEdit,
      emptySection,
      selected,
      checklist,
      getSelectedListIndex
    }

  },
  methods: {
    addList() {
      const emptyList = {
        "Title": "Example",
        "Hidden": false,
        "Completed": 0,
        "Desc": "",
        "Edit": false,
        "Data": Array()
      }
      this.checklist.Data.push(emptyList)
    },
    removeList(index: number) {
      this.checklist.Data.splice(index, 1)
    },

    addSection() {
      const emptyList = {
        "Title": "Example",
        "Hidden": false,
        "Completed": 0,
        "Desc": "",
        "Edit": false,
        "Data": Array()
      }

      let section = {
        "Title": "Section Title",
        "Hidden": true,
        "Edit": false,
        "Data": Array()
      }
      section.Data.push(emptyList)
      this.checklist.Data[this.getSelectedListIndex()].Data.push(section)
    },

    addItem(checklist: any, section: any) {
      const emptyItem = {
        "Name": "Item Title",
        "Type": "",
        "Value": false,
        "ToDo": "",
        "Desc": ""
      }
      section.Data.push(emptyItem)
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

  ul {
    li {
      display: flex;
      padding-right: 40px !important;
      position: relative;
      input {
        padding-top: 0;
        padding-bottom: 0;
      }
      .remove {
        transition: all 0.3s;
        &:hover {
          color: red;
        }
      }
      .edit-icons {
        cursor: pointer;
        position: absolute;
        right: 10px;
      }
    }
  }
}

.checklist-main .checklist .checklist-parent {
  .checklist-section.visible {
    .add-item {
      float: right;
      margin-bottom: 20px;
    }
  }
  .title {
    cursor: default;
    position: relative;
    padding-right: 65px;
    .edit {
      top: 0;
      height: 100%;
      padding-top: 10px;
      text-align: center;
      cursor: pointer;
      position: absolute;
      right: 20px;
      opacity: 0.8;
      transition: all 0.3s;
      &:hover {
        opacity: 1;
      }
    }
  }
}

.checklist-items {
  text-align: left;
  .form-group.row {
    margin-bottom: 1rem;
  }
}
</style>