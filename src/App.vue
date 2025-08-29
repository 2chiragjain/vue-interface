<template>
  <div id="main-app" class="container" >
    <div class="row justify-content-center">
      <AddAppointment @add="addItem"/>
      <search-appointments @searchRecords="searchAppointments" 
        :myKey="filterKey" 
        :myDir="filterDir"
        @requestKey="requestKey"
        @requestDir="requestDir"/>
      <appointmentlist :appointments="filteredApts"
       @remove="removeItem" @edit="editItem" />
    </div>
  </div>  
</template>

<script>
// import {FontAwesomeIcon} from "@fortawesome/vue-fontawesome"
import axios from "axios"
import Appointmentlist from "./components/AppointmentList";
import _ from "lodash";
import AddAppointment from "./components/AddAppointment.vue";
import SearchAppointments from "./components/SearchAppointments.vue";

export default {
  name: 'MainApp',
  data: function() {
    return {      
      appointments: [],
      filterKey: "petName",
      filterDir: "asc",
      searchTerms:"",
      aptIndex: 0
    };
  },
  components:{
    Appointmentlist,
    AddAppointment,
    SearchAppointments
  },
  mounted() {
    axios
    .get("/data/appointments.json")
    .then(response => (this.appointments = response.data.map(item=> {
      item.aptID = this.aptIndex;
      this.aptIndex++;
      return item
    })));
  },
  methods: {
    removeItem: function(apt){
      this.appointments=_.without(this.appointments,apt);
    },
    editItem: function(id, field, text) {
      const aptIndex = _.findIndex(this.appointments, {
        aptID: id
      });
      this.appointments[aptIndex][field] = text;
    },
    addItem: function (apt) {
      apt.aptID = this.aptIndex;
      this.aptIndex++;
      this.appointments.push(apt);
    },
    searchAppointments: function(terms) {
      this.searchTerms = terms;      
    },
    requestKey: function(key) {
      this.filterKey=key
    },
    requestDir: function(dir) {
      this.filterDir=dir
    }
  },
  computed: {
    searchedAppointments: function() {
      return this.appointments.filter(item =>{
        return (
          item.petName.toLowerCase().match(this.searchTerms.toLowerCase()) ||
          item.petOwner.toLowerCase().match(this.searchTerms.toLowerCase()) ||
          item.aptNotes.toLowerCase().match(this.searchTerms.toLowerCase())
        )
      })
    },
    filteredApts: function() {
      return _.orderBy(
        this.searchedAppointments,
        item => {
          return item[this.filterKey].toLowerCase();
        }, this.filterDir
      );
    }
  }
}
</script>

