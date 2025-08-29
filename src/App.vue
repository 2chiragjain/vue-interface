<template>
  <div id="main-app" class="container" >
    <div class="row justify-content-center">
      <AddAppointment @add="addItem"/>
      <appointmentlist :appointments="appointments"
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

export default {
  name: 'MainApp',
  data: function() {
    return {
      title: "Appointment List",
      appointments: [],
      aptIndex: 0
    };
  },
  components:{
    Appointmentlist,
    AddAppointment
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
    }
  }
}
</script>

