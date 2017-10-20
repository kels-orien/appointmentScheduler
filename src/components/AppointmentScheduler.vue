<template>
<v-app light>
  <v-toolbar dark color="primary">
    <v-toolbar-side-icon></v-toolbar-side-icon>
    <v-toolbar-title class="white--text">AppointmentScheduler</v-toolbar-title>
  </v-toolbar>
  <v-layout row justify-center>
    <v-container fluid>

      <v-flex x12 sm6 offset-sm3>
        <v-card>
          <v-stepper v-model="e6" vertical>
            <v-stepper-step step="1" v-bind:complete="e6 > 1" complete editable>
              Choose a day for your appointment</v-stepper-step>

            <v-stepper-content step="1">

              <v-flex xs11 sm5>
                <v-dialog persistent v-model="modal" lazy full-width>
                  <v-text-field slot="activator" label="Picker in dialog" v-model="date" prepend-icon="event" readonly></v-text-field>
                  <v-date-picker v-model="date" :allowed-dates="allowedDates" scrollable actions>
                    <template scope="{ save, cancel }">
              <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn flat color="primary" @click="cancel">Cancel</v-btn>
                <v-btn flat color="primary"  @click.native="e6 = 2"  @click="save">OK</v-btn>
              </v-card-actions>
            </template>
                  </v-date-picker>
                </v-dialog>
              </v-flex>
            </v-stepper-content>
            <v-stepper-step step="2" complete editable v-bind:complete="e6 > 2">Choose an available time for your appointment</v-stepper-step>
            <v-stepper-content step="2">
              <v-flex xs11 sm5>
                <v-select
                v-bind:items="items"
                v-model="e1"
                label="Select"
                item-value="text"
                single-line
                bottom
              ></v-select>
              </v-flex>
              <v-radio-group v-model="selected"  v-show="ok"  v-on:change="selectedTime">
                <v-radio label="9:00am - 10:00 am" @click.native="e6 = 3" value="9:00am - 10:00 am"></v-radio>
                <v-radio label="10:00am - 11:00 am" @click.native="e6 = 3" value="10:00am - 11:00 am"></v-radio>
                <v-radio label="11:00 am - 12:00 pm" @click.native="e6 = 3" value="11:00 am - 12:00 pm"></v-radio>
              </v-radio-group>
              <v-radio-group  v-model="selected" v-show="collapsed" v-on:change="selectedTime">
                <v-radio label="12:00 pm - 1:00 pm" @click.native="e6 = 3" value="12:00 pm - 1:00 pm"></v-radio>
                <v-radio label="1:00 pm - 2:00 pm" @click.native="e6 = 3" value="1:00 pm - 2:00 pm"></v-radio>
                <v-radio label="2:00 pm - 3:00 pm" @click.native="e6 = 3" value="2:00 pm - 3:00 pm"></v-radio>
                <v-radio label="3:00 pm - 4:00 pm" @click.native="e6 = 3" value="3:00 pm - 4:00 pm"></v-radio>
                <v-radio label="4:00 pm - 5:00 pm" @click.native="e6 = 3" value="4:00 pm - 5:00 pm"></v-radio>
              </v-radio-group>
            </v-stepper-content>
            <v-stepper-step step="3" complete editable v-bind:complete="e6 > 3">Share your contact information with us and we 'll send you a reminder</v-stepper-step>
            <v-stepper-content step="3">
              <v-form v-model="valid" ref="form" lazy-validation>
              <v-text-field name="firstname" label="First Name" value=""  v-model="firstname" :rules="firstnameRules" :counter="15" required/>
              <v-text-field name="lastname" label="Last Name"  v-model="lastname" :rules="lastnameRules" :counter="15" required/>
              <v-text-field name="email" label="Email" v-model="email":rules="emailRules" required/>
              <v-text-field name="phone" label="Phone" v-model="phone":rules="phoneRules" required/>
                <v-dialog v-model = "dialog" persistent max-width ="750">
                <v-btn block color="primary" dark slot="activator">SCHEDULE</v-btn>
                <v-card>
                  <div v-for="user in userArray">
                  <v-flex xs12 sm6 md4>
                    <span>Firstname: {{user.firstname}}</span>
                  </v-flex>
                  <v-flex xs12 sm6 md4>
                    <span>Lastname: {{user.lastname}}</span>
                  </v-flex>
                  <v-flex xs12 sm6 md4>
                    <span>Email: {{user.email}}</span>
                  </v-flex>
                  <v-flex xs12 sm6 md4>
                    <span>Phone: {{user.phone}}</span>
                  </v-flex>
                </div>
                  <v-card-actions>
                     <v-spacer></v-spacer>
                     <v-btn color="blue darken-1" flat @click.native="dialog = false" @click="submit">Confirm</v-btn>
                   </v-card-actions>
              </v-card>
            </v-dialog>

              </v-form>
            </v-stepper-content>
          </v-stepper>


        </v-card>

      </v-flex>
    </v-container>
  </v-layout>
  <v-footer app></v-footer>
</v-app>
</template>

<script>
export default {
  name: 'AppointmentScheduler',
  data () {
    return {
      msg: 'Welcome to Your Vue.js App',
      date: null,
      time: null,
      modal: null,
      modal2: null,
      dialog: false,
      ex8: null,
      e6: null,
      e1: null,
      userArray: [],
      allowedDates: null,
      select: null,
      selected: null,
      ok: null,
      collapsed: null,
      valid: null,
      firstname: '',
      firstnameRules: [
        (v) => !!v || 'First Name is required',
        (v) => v.length <= 15 || 'First Name must be less than 15 characters'
      ],
      lastname: '',
      lastnameRules: [
        (v) => !!v || 'Last Name is required',
        (v) => v.length <= 15 || 'Last Name must be less than 15 characters'
      ],
      email: '',
      emailRules: [
        (v) => !!v || 'E-mail is required',
        (v) => /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || 'E-mail must be valid'
      ],
      phone: '',
      phoneRules: [
        (v) => !!v || 'Phone number is required',
        (v) => /^(1\s|1|)?((\(\d{3}\))|\d{3})(-|\s)?(\d{3})(-|\s)?(\d{4})$/.test(v) || 'Phone number must be valid'
      ],
      items: [
        { text: 'AM' },
        { text: 'PM' }
      ],
      availableDays: {
        min: null,
        max: null
      }
    }
  },
  mounted () {
    const nowdate = new Date()
    const todaydate = new Date()
    const endofyear = new Date(todaydate.getFullYear(), 12, 31)
    const singleday = 1000 * 60 * 60 * 24
    const remainingdays = Math.ceil((endofyear.getTime() - todaydate.getTime()) / singleday)
    todaydate.setDate(nowdate.getDate() + remainingdays)
    this.availableDays.min = nowdate
    this.availableDays.max = todaydate

    this.allowedDates = this.availableDays
  },
  beforeUpdate () {
    if (this.e1 === 'AM') {
      this.ok = true
      this.collapsed = null
      console.log('value', this.e1)
      this.e1 = null
    } else if (this.e1 === 'PM') {
      this.collapsed = true
      this.ok = null
      console.log('value', this.e1)
      this.e1 = null
    }
  },
  methods: {
    selectedTime () {
      console.log('Time for appointment:', this.selected)
    },
    submit () {
      this.$validator.validateAll()
      this.userArray = {'firstname': this.firstname, 'lastname': this.lastname, 'email': this.email, 'phone': this.phone}
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1,
h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.is-collapsed {
  display: none;
}
</style>
