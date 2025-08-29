<template>
  <div id="main-app" class="container" >
    <div class="row justify-content-center">
      <appointmentlist :appointments="appointments" @remove="removeItem"/>
    </div>
    
    

  </div>  
</template>

<script>
// import {FontAwesomeIcon} from "@fortawesome/vue-fontawesome"
import axios from "axios"
import Appointmentlist from "./components/AppointmentList";
import _ from "lodash";
export default {
  name: 'MainApp',
  data: function() {
    return {
      title: "Appointment List",
      appointments: []
    };
  },
  components:{
    Appointmentlist
  },
  mounted() {
    axios
    .get("/data/appointments.json")
    .then(response => (this.appointments = response.data));
  },
  methods: {
    removeItem: function(apt){
      this.appointments=_.without(this.appointments,apt);
    }
  }
}
</script>

