<style scoped>
::v-deep .costomgray > .v-input__control > .v-input__slot {
  background-color: white;
}
</style>
<template>
  <div>
    <v-card elevation="1" class="mt-3">
      <v-card-title>
        <b><v-icon>mdi-bed-outline</v-icon> ห้องรักษา&nbsp;</b>
        <v-chip color="error" label outlined>
          ทั้งหมด {{ desserts.length }} รายการ
        </v-chip>
        <v-spacer />
        <div class="text-center">
          <v-btn
            text
            color="success"
            @click="
              dialogTitle = 'เพิ่มห้องรักษา';
              dialog = true;
            "
          >
            <v-icon>mdi-bed-outline</v-icon> เพิ่ม
          </v-btn>
        </div>
      </v-card-title>
      <v-divider />
      <v-card-text>
        <v-card color="rgb(237 235 215)" elevation="0" class="pa-5 mb-3">
          <v-row>
            <v-col md="11">
              <v-text-field
                class="costomgray"
                prepend-inner-icon="mdi-magnify"
                outlined
                dense
                placeholder="ห้องรักษา รายละเอียด"
                hide-details
                v-model="textSearch"
              ></v-text-field>
            </v-col>
            <v-col md="1">
              <v-btn elevation="0" color="primary" @click="fn_getData">
                <v-icon>mdi-magnify</v-icon>
                ค้นหา
              </v-btn>
            </v-col>
          </v-row>
        </v-card>
        <br />
        <v-divider style="border-top: 1px dashed rgba(0, 0, 0, 0.12)" />
        <v-simple-table fixed-header height="70vh">
          <template v-slot:default>
            <thead>
              <tr>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                >
                  ลำดับ
                </th>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                >
                  เลขห้อง
                </th>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                >
                  ห้องรักษา
                </th>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                >
                  รายละเอียด
                </th>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                >
                  สถานะ
                </th>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                >
                  Action
                </th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(r, i) in desserts" :key="i">
                <td class="text-center" nowrap>{{ i + 1 }}</td>
                <td nowrap class="text-left">{{ r.Room_Number }}</td>
                <td nowrap class="text-left">{{ r.Room_Name }}</td>
                <td nowrap class="text-left">{{ r.Room_Detail }}</td>
                <td nowrap class="text-left">{{ r.Room_Status }}</td>
                <td nowrap class="text-left">
                  <v-btn
                    elevation="0"
                    color="error"
                    small
                    text
                    @click="
                      FormAdd.ID_room = r.ID_room;
                      FormAdd.Room_Number = r.Room_Number;
                      FormAdd.Room_Name = r.Room_Name;
                      FormAdd.Room_Detail = r.Room_Detail;
                      dialogTitle = 'แก้ไขห้องรักษา';
                      dialog = true;
                    "
                  >
                    <v-icon>mdi-text-box-edit</v-icon>
                  </v-btn>
                </td>
              </tr>
            </tbody>
          </template>
        </v-simple-table>
      </v-card-text>
    </v-card>

    <v-dialog v-model="dialog" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="text-h5"
            ><v-icon>mdi-calendar-plus-outline</v-icon> {{ dialogTitle }}</span
          >
        </v-card-title>
        <v-card-text>
          <v-row>
            <v-col md="6" sm="12" cols="12">
              <v-text-field
                label="เลขห้อง"
                outlined
                dense
                hide-details
                v-model="FormAdd.Room_Number"
              ></v-text-field>
            </v-col>
            <v-col md="6" sm="12" cols="12">
              <v-text-field
                label="ห้องรักษา"
                outlined
                dense
                hide-details
                v-model="FormAdd.Room_Name"
              ></v-text-field>
            </v-col>
            <v-col md="12" sm="12" cols="12">
              <v-text-field
                label="รายละเอียด"
                outlined
                dense
                hide-details
                v-model="FormAdd.Room_Detail"
              ></v-text-field>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="dialog = false">
            Close
          </v-btn>
          <v-btn
            v-if="FormAdd.ID_room"
            color="blue darken-1"
            text
            @click="fn_upadateroom"
          >
            Save
          </v-btn>
          <v-btn v-else color="blue darken-1" text @click="fn_insertroom">
            Save
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </div>
</template>
  
<script>
import axios from "axios";
export default {
  name: "InspirePage",
  data: () => ({
    dialog: false,
    dialogTitle: "",
    skeletonshow: true,
    countdata: 0,
    page: 1,
    total_record: 1,
    desserts: [],
    FormAdd: {
      ID_room: "",
      Room_Number: "",
      Room_Name: "",
      Room_Detail: "",
      Room_Status: "",
    },
    textSearch: "",
  }),
  methods: {
    async fn_getData() {
      await axios
        .get(`${process.env.api_url}/room?textSearch=${this.textSearch}`, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.desserts = res.data.data;
        })
        .catch((err) => {
          alert(err);
        });
    },
    async fn_insertroom() {
      let data = JSON.stringify(this.FormAdd);
      await axios
        .post(`${process.env.api_url}/room/insert`, data, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.dialog = false;
          this.FormAdd = {
            ID_room: "",
            Room_Number: "",
            Room_Name: "",
            Room_Detail: "",
            Room_Status: "",
          };
          this.fn_getData();
        })
        .catch((err) => {
          alert(err);
        });
    },
    async fn_upadateroom() {
      let data = JSON.stringify(this.FormAdd);
      await axios
        .post(`${process.env.api_url}/room/update`, data, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.dialog = false;
          this.FormAdd = {
            ID_room: "",
            Room_Number: "",
            Room_Name: "",
            Room_Detail: "",
            Room_Status: "",
          };
          this.fn_getData();
        })
        .catch((err) => {
          alert(err);
        });
    },
  },
  mounted() {
    this.fn_getData();
  },
};
</script>
  