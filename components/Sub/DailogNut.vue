<template>
  <v-dialog v-model="dialog" persistent max-width="600px">
    <v-card>
      <v-card-title>
        <span class="text-h5"
          ><v-icon>mdi-calendar-plus-outline</v-icon> เพิ่มนัด</span
        >
      </v-card-title>
      <v-card-text>
        <v-row>
          <v-col md="6" sm="12" cols="12">
            <v-menu
              v-model="menu"
              :close-on-content-click="false"
              :nudge-right="40"
              transition="scale-transition"
              offset-y
              min-width="auto"
            >
              <template v-slot:activator="{ on, attrs }">
                <v-text-field
                  v-model="FormAdd.Date_nut"
                  label="วันที่"
                  prepend-inner-icon="mdi-calendar"
                  readonly
                  v-bind="attrs"
                  v-on="on"
                  outlined
                  dense
                  hide-details
                  class="costomgray"
                ></v-text-field>
              </template>
              <v-date-picker
                v-model="FormAdd.Date_nut"
                color="deep-orange"
                @input="menu = false"
              ></v-date-picker>
            </v-menu>
            <!-- <Vmenu  v-bind:vdate="FormAdd.Date_nut" :label="'วันที่'" /> -->
          </v-col>
        </v-row>
        <v-row class="pb-3">
          <v-col md="6" sm="12" cols="12">
            <v-text-field
              label="เริ่มเวลา"
              outlined
              dense
              hide-details
              type="time"
              v-model="FormAdd.start_time"
            ></v-text-field>
          </v-col>
          <v-col md="6" sm="12" cols="12">
            <v-text-field
              label="สิ้นสุด"
              outlined
              dense
              hide-details
              type="time"
              v-model="FormAdd.end_time"
            ></v-text-field>
          </v-col>
          <v-col md="6" sm="12" cols="12">
            <v-select
              outlined
              dense
              hide-details
              placeholder="-----เลือกแพทย์-----"
              :items="DoctorOP"
              v-model="FormAdd.ID_doctor"
            ></v-select>
          </v-col>
          <v-col md="6" sm="12" cols="12">
            <v-select
              outlined
              dense
              hide-details
              placeholder="----- ห้อง -----"
              :items="RoomOP"
              v-model="FormAdd.ID_room"
            ></v-select>
          </v-col>
          <v-col md="6" sm="12" cols="12">
            <v-select
              outlined
              dense
              hide-details
              placeholder="----- เลือกการรักษา -----"
              :items="PackageOP"
              v-model="FormAdd.ID_package"
            ></v-select>
          </v-col>
          <v-col md="12" sm="12" cols="12">
            <v-textarea
              outlined
              dense
              hide-details
              label="หมายเหตุ"
              rows="3"
              v-model="FormAdd.Remark"
            ></v-textarea>
          </v-col>
        </v-row>
        <v-divider />
        <v-row class="pt-3">
          <v-col md="6" sm="12" cols="12">
            <v-autocomplete
              :items="CoustomerOP"
              placeholder="ชื่อ หรือ รหัส คนไข้"
              outlined
              dense
              hide-details
              @change="fn_changeCusOP"
            ></v-autocomplete>
          </v-col>
        </v-row>
        <v-row>
          <v-col md="6" sm="12" cols="12">
            <v-text-field
              label="ชื่อคนไข้"
              outlined
              dense
              hide-details
              v-model="FormAdd.name"
            ></v-text-field>
          </v-col>
          <v-col md="6" sm="12" cols="12">
            <v-text-field
              label="เบอร์โทร"
              outlined
              dense
              hide-details
              v-model="FormAdd.tel"
            ></v-text-field>
          </v-col>
        </v-row>
      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="blue darken-1" text @click="dialog = false">
          Close
        </v-btn>
        <v-btn color="blue darken-1" text @click="fn_insertNut"> Save </v-btn>
      </v-card-actions>
    </v-card>
  </v-dialog>
</template>
<script>
import axios from "axios";
import Vmenu from "@/components/Vmenu";
export default {
  name: "IndexPage",
  components: {
    Vmenu,
  },
  data() {
    return {
      menu: false,
      dialog: false,
      FormAdd: {
        Date_nut: new Date(Date.now() - new Date().getTimezoneOffset() * 60000)
          .toISOString()
          .substr(0, 10),
        ID_customer: "",
        name: "",
        tel: "",
        start_time: "",
        end_time: "",
        ID_doctor: "",
        ID_room: "",
        ID_package: "",
        Remark: "",
      },
      CoustomerOP: [],
      DoctorOP: [],
      RoomOP: [],
      PackageOP: [],
      Coustomer: {},
    };
  },
  methods: {
    async fn_dropdownCus() {
      this.CoustomerOP = [];
      await axios
        .get(`${process.env.api_url}/customer?textSearch=&IDCus=`, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          res.data.data.forEach((e) => {
            this.CoustomerOP.push({
              text: `${e.ID_customer} - ${e.Fisrtname} ${e.Lastname}`,
              value: {
                ID_customer: e.ID_customer,
                name: `${e.Fisrtname} ${e.Lastname}`,
                tel: e.tell,
              },
            });
          });
        })
        .catch((err) => {
          alert(err);
        });
    },
    async fn_dropdownDoctor() {
      await axios
        .get(`${process.env.api_url}/doctor?textSearch=&ID=`, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          res.data.data.forEach((e) => {
            this.DoctorOP.push({
              text: `${e.Fisrtname} ${e.Lastname}`,
              value: e.ID,
            });
          });
        })
        .catch((err) => {
          alert(err);
        });
    },
    async fn_dropdownRoom() {
      await axios
        .get(`${process.env.api_url}/room?textSearch=`, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          res.data.data.forEach((e) => {
            this.RoomOP.push({
              text: `${e.Room_Name} ${e.Room_Number}`,
              value: e.ID_room,
            });
          });
        })
        .catch((err) => {
          alert(err);
        });
    },
    async fn_dropdownPackage() {
      await axios
        .get(`${process.env.api_url}/package?textSearch=`, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          res.data.data.forEach((e) => {
            this.PackageOP.push({
              text: `${e.treat_name}`,
              value: e.ID_treat,
            });
          });
        })
        .catch((err) => {
          alert(err);
        });
    },
    fn_changeCusOP(value) {
      this.FormAdd.ID_customer = value.ID_customer;
      this.FormAdd.name = value.name;
      this.FormAdd.tel = value.tel;
    },
    async open() {
      await this.fn_dropdownCus();
      await this.fn_dropdownDoctor();
      await this.fn_dropdownRoom();
      await this.fn_dropdownPackage();
      this.dialog = true;
    },
    async fn_insertNut() {
      let data = JSON.stringify(this.FormAdd);
      await axios
        .post(`${process.env.api_url}/appointment/insert`, data, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          FormAdd = {
            Date_nut: new Date(
              Date.now() - new Date().getTimezoneOffset() * 60000
            )
              .toISOString()
              .substr(0, 10),
            ID_customer: "",
            name: "",
            tel: "",
            start_time: "",
            end_time: "",
            ID_doctor: "",
            ID_room: "",
            ID_package: "",
            Remark: "",
          };
          this.dialog = false;
          this.$emit("getdata");
        })
        .catch((err) => {
          alert(err);
        });
    },
  },
};
</script>