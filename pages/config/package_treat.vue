<style scoped>
::v-deep .costomgray > .v-input__control > .v-input__slot {
  background-color: white;
}
</style>
<template>
  <div>
    <v-card elevation="1" class="mt-3">
      <v-card-title>
        <b><v-icon>mdi-medication</v-icon> หัวข้อการรักษา&nbsp;</b>
        <v-chip color="error" label outlined>
          ทั้งหมด {{ desserts.length }} รายการ
        </v-chip>
        <v-spacer />
        <div class="text-center">
          <v-btn
            text
            color="success"
            @click="
              dialogTitle = 'เพิ่มหัวข้อการรักษา';
              dialog = true;
            "
          >
            <v-icon>mdi-medication</v-icon> เพิ่ม
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
                placeholder="หัวข้อการรักษา รายละเอียด"
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
                  หัวข้อการรักษา
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
                <td nowrap class="text-left">{{ r.treat_name }}</td>
                <td nowrap class="text-left">{{ r.treat_detail }}</td>
                <td nowrap class="text-left">{{ r.treat_status }}</td>
                <td nowrap class="text-left">
                  <v-btn
                    elevation="0"
                    color="error"
                    small
                    text
                    @click="
                      FormAdd.ID_treat = r.ID_treat;
                      FormAdd.treat_name = r.treat_name;
                      FormAdd.treat_detail = r.treat_detail;
                      dialogTitle = 'แก้ไขหัวข้อการรักษา';
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
            <v-col md="12" sm="12" cols="12">
              <v-text-field
                label="หัวข้อ"
                outlined
                dense
                hide-details
                v-model="FormAdd.treat_name"
              ></v-text-field>
            </v-col>
            <v-col md="12" sm="12" cols="12">
              <v-text-field
                label="รายละเอียด"
                outlined
                dense
                hide-details
                v-model="FormAdd.treat_detail"
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
            v-if="FormAdd.ID_treat"
            color="blue darken-1"
            text
            @click="fn_upadatepackage"
          >
            Save
          </v-btn>
          <v-btn v-else color="blue darken-1" text @click="fn_insertpackage">
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
      ID_treat: "",
      treat_name: "",
      treat_detail: "",
      treat_status: "",
    },
    textSearch: "",
  }),
  methods: {
    async fn_getData() {
      await axios
        .get(`${process.env.api_url}/package?textSearch=${this.textSearch}`, {
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
    async fn_insertpackage() {
      let data = JSON.stringify(this.FormAdd);
      await axios
        .post(`${process.env.api_url}/package/insert`, data, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.FormAdd = {
            treat_name: "",
            treat_name: "",
            treat_detail: "",
            treat_status: "",
          };
          this.dialog = false;
          this.fn_getData();
        })
        .catch((err) => {
          alert(err);
        });
    },
    async fn_upadatepackage() {
      let data = JSON.stringify(this.FormAdd);
      await axios
        .post(`${process.env.api_url}/package/update`, data, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.FormAdd = {
            treat_name: "",
            treat_name: "",
            treat_detail: "",
            treat_status: "",
          };
          this.dialog = false;
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
  