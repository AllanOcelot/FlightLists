<template>
  <div class="introduction-banner left">
    <div class="container">
      <div class="row">
        <div class="col">
          <h1>Create Checklist:</h1>
          <p>Please use the following tool to create your own Checklist data in JSON. <br>
          The resulting checklist can be submitted to FlightLists, making a greater experience for everyone!</p>
        </div>
      </div>
    </div>
  </div>


  <div class="list-container">
    <div class="container">
      <div class="row">
        <div class="col">
          <div class="content-wrapper">
              <label :for="defaultData.Name" class="form-label">Aircraft Name:</label>
              <input type="text" class="form-control" :id="defaultData.Name" v-model="defaultData.Name">
              <label :for="defaultData.Desc" class="form-label">Description:</label>
              <input type="text" class="form-control" :id="defaultData.Desc" v-model="defaultData.Desc">

              <div class="type-selection">
                <p class="title">
                  Type of Checklist to edit:
                  <span 
                    class="add"
                    @click="addType()"
                  >
                    Add Type
                    <i class="bi bi-plus"></i>
                  </span>
                </p>
                <ul class="nav nav-tabs">
                  <li class="nav-item"
                    v-for="data in defaultData.Data" :key="data.Title"
                    @click="selected = data.Title"
                    :class="{active: selected === data.Title}"
                  >
                    <span class="nav-link">
                      {{data.Title}}
                    </span>
                  </li>
                </ul>
              </div>


              <div v-for="data in defaultData.Data" :key="data.Name">
                <div v-if="selected === data.Title">
                  <label :for="data.Title" class="form-label">Section Name:</label>
                  <input type="text" class="form-control" :id="data.Title" v-model="data.Title">

                
                  <div class="type" v-for="type in data.Data" :key="type.Title">
                    {{type}}

                    <label :for="type.Title" class="form-label">Sub Section Name:</label>
                    <input type="text" class="form-control" :id="type.Title" v-model="type.Title">


                      <div class="section" v-for="section in type.Data" :key="section.Title">
                        <div class="item" v-for="item in section.Data" :key="item.Name">
                        </div>
                        <!-- <button type="button" class="btn btn-primary">Add Item</button> -->
                      </div>
                      <!-- <button type="button" class="btn btn-primary">Add Section</button> -->
                  </div>

                  <button 
                    type="button" 
                    class="btn btn-outline-secondary add"
                    @click="addSection()"
                  >
                    Add Section
                    <i class="bi bi-plus"></i>
                  </button>
                </div>







              </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Create',
  data: function(){
    return {
      loading: true, 
      loadingError: false,
      selected: 'PreFlight',
      defaultData: {
        "Name": "Enter Aircraft name",
        "Desc": "A short description here, summarise the aircraft",
        "Author": "Example: Joe Boss",
        "Data": [
        {
          "Title": "PreFlight",
          "Desc": "Example: Checks to be carried out at PreFlight",
          "Completed": false,
          "Data": 
          [
            {
              "Title": "Example: Cabin",
              "Hidden": true,
              "Data": 
              [
              {
                "Name": "Example: Documents",
                "Type": "Text",
                "Value": false,
                "ToDo": "Example: A.R.R.O.W",
                "Desc": "Example: Remember to check A.R.R.O.W"
              }
            ]
          }
      ]}
    ]}
  }
  },
  components: {

  },
  methods: {
    addType(){
      const newData = {
          "Title": "Enter Section Name",
          "Desc": "Enter Section Description",
          "Completed": false,
          "Data": 
          [
            {
              "Title": "Enter Title",
              "Hidden": true,
              "Data": 
              [
              {
                "Name": "Enter Name",
                "Type": "Text",
                "Value": false,
                "ToDo": "Enter Action to do",
                "Desc": "Enter description / additional information"
              }
            ]
          }
      ]
      }
      this.defaultData.Data.push(newData)
    },


    addSection(){
      const newData =       
      {
        "Title": "Example: Cabin",
        "Hidden": true,
        "Data": 
        [
          {
            "Name": "Example: Documents",
            "Type": "Text",
            "Value": false,
            "ToDo": "Example: A.R.R.O.W",
            "Desc": "Example: Remember to check A.R.R.O.W"
          }
        ]
      }
      

    // Get the selection checlist Type,
    // Then add the new section to that.
    let ChecklistToEdit = this.defaultData.Data.filter(obj => {
      return obj.Title === this.selected
    })
    console.log(newData)
    console.log(ChecklistToEdit)
    ChecklistToEdit[0].Data.push(newData)

    //this.defaultData.Data.push(newData)

    },
  }
};
</script>

<style lang="scss" scoped>
    @import "../assets/vars.scss";
    @import "../assets/common.scss";


    .list-container {
      .info {
        background: $color-white;
        text-align: left;
      }

      .type-selection {
        margin: 40px 0;
        padding: 40px 0 0 0;
        border-top: 2px solid $color-grey;
        border-bottom: 2px solid $color-grey;
        .title {
          font-size: 1.2em;
        }
        .add {
          float: right;
        }        
      }

      .content-wrapper {
        background: $color-white;
        text-align: left;
        padding: 20px;

        label {
          margin-top: 10px;
        }
      }
    }

</style>