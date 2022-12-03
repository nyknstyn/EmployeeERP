<template>
  <section>
    <p v-if="$fetchState.pending">Fetching mountains...</p>
    <p v-else-if="$fetchState.error">An error occurred :(</p>
    <div v-else>
      <div class="container">
        <b-field grouped group-multiline>
          <b-button
            label="Clear checked"
            type="is-light"
            icon-left="close"
            :disabled="checkedRows.length === 0"
            class="field"
            @click="checkedRows = []" />

          <b-button
            type="is-danger"
            icon-right="delete"
            :disabled="checkedRows.length === 0"
            class="field"
            :loading="isPopupWaiting"
            @click="delete_rows">
            Delete
          </b-button>

          <b-button
            label="Update"
            type="is-primary"
            icon-left="close"
            :disabled="!(checkedRows.length === 1)"
            class="field"
            @click="isUpdateMode = true" />

          <b-button
            label="Create"
            type="is-success"
            icon-left="close"
            :disabled="!(checkedRows.length === 0)"
            class="field"
            @click="isCreateMode = true" />

        </b-field>

        <!-- Create/Update Popup -->
        <b-modal
          v-model="isUpdateMode"
          has-modal-card
          trap-focus
          :destroy-on-hide="true"
          aria-role="dialog"
          aria-label="Example Modal"
          close-button-aria-label="Close"
          aria-modal>
          <template #default="props">
            <CRUDPopup :id="checkedRows[0].id" :name="checkedRows[0].departmentName" :capacity="checkedRows[0].capacity" :working="isPopupWaiting" label="Update" @close="isUpdateMode=false"></CRUDPopup>
          </template>
        </b-modal>

        <b-modal
          v-model="isCreateMode"
          has-modal-card
          trap-focus
          :destroy-on-hide="true"
          aria-role="dialog"
          aria-label="Example Modal"
          close-button-aria-label="Close"
          aria-modal>
          <template #default="props">
            <CRUDPopup :working="isPopupWaiting" label="Create" @close="isCreateMode=false"></CRUDPopup>
          </template>
        </b-modal>

        <b-tabs>
          <b-tab-item :label="tableName">
            <b-table
              :data="departmentData"
              :columns="departmentColumns"
              :checked-rows.sync="checkedRows"
              checkable
              detailed
              :checkbox-position="checkboxPosition"
              :checkbox-type="checkboxType">


              <!-- Employee Details -->
              <template #detail="props">
                <b-tabs>
                  <b-tab-item label="Employee">
                    <b-table
                      :data="props.row.employees"
                      :columns="employeeColumns">
                    </b-table>
                  </b-tab-item>

                  <!--      <b-tab-item label="Checked rows">-->
                  <!--        <pre>{{ checkedRows }}</pre>-->
                  <!--      </b-tab-item>-->
                </b-tabs>
              </template>

              <template #bottom-left>
                <b>Total checked</b>: {{ checkedRows.length }}
              </template>
            </b-table>
          </b-tab-item>

          <!--      <b-tab-item label="Checked rows">-->
          <!--        <pre>{{ checkedRows }}</pre>-->
          <!--      </b-tab-item>-->
        </b-tabs>
      </div>
    </div>
  </section>
</template>

<script>
import { departmentFullData } from '../service/test_data.js'
import { BE_URL, AUTHORIZATION_TOKEN } from '../service/constants.js'
import CRUDPopup from '@/components/CRUDPopup'

console.log("print")
console.log(departmentFullData)
export default {

  name: 'DataList',
  components: {CRUDPopup},
  data() {

    // const departmentData = departmentFullData

    return {
      departmentData:[],
      be_url: BE_URL,
      auth_key: AUTHORIZATION_TOKEN,
      tableName: 'Department',
      checkboxPosition: 'left',
      checkboxType: 'is-primary',
      checkedRows: [],
      isCreateMode: false,
      isUpdateMode: false,
      isPopupWaiting: false,
      departmentColumns: [
        {
          field: 'id',
          label: 'ID',
          width: '40',
          numeric: true,
          centered: true
        },
        {
          field: 'departmentName',
          label: 'Department Name',
          centered: true
        },
        {
          field: 'capacity',
          label: 'Capacity',
          centered: true
        }
      ],
      employeeColumns: [
        {
          field: 'id',
          label: 'ID',
          width: '40',
          numeric: true,
          centered: true
        },
        {
          field: 'firstName',
          label: 'First Name',
          centered: true
        },
        {
          field: 'lastName',
          label: 'Last Name',
          centered: true
        },
        {
          field: 'email',
          label: 'Email',
          centered: true
        }
      ]
    }
  },
  async fetch() {
    let url = "/connect/v1/testAPI"
    const config = {
      headers:{
        'Authorization': this.auth_key,
        'app-id': '5cc33938dfa60d49ef36a5e4',
        'Content-Type':'application/json'
      }
    };

    let responseData = {}
    await this.$axios.$get(url,config)
      .then((response)=>{
        responseData = response
        console.log(response)
      })
      .catch(function (error){
        console.log("Something failed")
        console.log(error);
        this.$buefy.toast.open('Something went wrong')
      })
    console.log("IT worked")
    this.departmentData = responseData["departments"]
    console.log(responseData)
  },
  fetchOnServer: false,
  methods: {
    async delete_rows() {
      this.isPopupWaiting = true
      let url = "/connect/v1/testAPI"

      const config = {
        headers:{
          'Authorization': this.auth_key,
          'app-id': '5cc33938dfa60d49ef36a5e4',
          'Content-Type':'application/json'
        }
      };

      // Delete Logic
      let responseData = {}
      for (let id in this.checkedRows){
        let department = this.checkedRows[id]
        await this.$axios.$delete(url + '/' + department.id, config)
          .then((response)=>{
              responseData = response
              console.log(this.checkedRows)
              console.log(department)
              console.log(this.departmentData.findIndex(item => item.id === department.id))
              this.departmentData.splice(this.departmentData.findIndex(item => item.id === department.id), 1)
            }
          )
          .catch(function (error){
            console.log("Something failed")
            console.log(error);
            this.$buefy.toast.open('Something went wrong')
          })
      }
      this.isPopupWaiting = false
      this.$buefy.toast.open('Deleted')
      this.checkedRows = []

    },
    async updateDepartment(evt) {
      this.isPopupWaiting = true

      let url = "/connect/v1/testAPI"


      const config = {
        headers:{
          'Authorization': this.auth_key,
          'app-id': '5cc33938dfa60d49ef36a5e4',
          'Content-Type':'application/json'
        }
      };

      let requestObject = {
        "departmentName": evt.name,
        "capacity": evt.capacity
      }

      // Update Logic
      let responseData = {}
      await this.$axios.$put(url + '/' + evt.id, requestObject, config)
        .then((response)=>{
          responseData = response
          // Create Logic
          let index = this.departmentData.findIndex(item => item.id === evt.id)
          let department = this.departmentData[index]
          department.departmentName = evt.name
          department.capacity = evt.capacity
          this.$buefy.toast.open('Updated')
          this.isPopupWaiting = false
          this.isUpdateMode = false
        })
        .catch(function (error){
          console.log("Something failed")
          console.log(error);
          this.isPopupWaiting = false
          this.$buefy.toast.open('Something went wrong')
        })
    },
    async createDepartment(evt) {
      this.isPopupWaiting = true

      let url = "/connect/v1/testAPI"
      // let url = this.be_url +  "/v1/236ecd9d-b423-4a33-b943-8ad940c45411"

      const config = {
        headers:{
          'Authorization': this.auth_key,
          'app-id': '5cc33938dfa60d49ef36a5e4',
          'Content-Type':'application/json'
        }
      };

      let requestObject = {
        "departmentName": evt.name,
        "capacity": evt.capacity
      }

      let responseData = {}

      await this.$axios.$post(url, requestObject, config)
        .then((response)=>{
          responseData = response
          // Create Logic
          this.departmentData.push(responseData)
          this.$buefy.toast.open('Created')
          this.isPopupWaiting = false
          this.isCreateMode = false
        })
        .catch(function (error){
          console.log("Something failed")
          console.log(error);
          this.isPopupWaiting = false
          this.$buefy.toast.open('Something went wrong')
        })

    },
  },
  mounted () {
    this.$nuxt.$on('EVENT_Update', this.updateDepartment)
    this.$nuxt.$on('EVENT_Create', this.createDepartment)
  }
}
</script>
