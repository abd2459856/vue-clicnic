<style scoped>
::v-deep .costomgray > .v-input__control > .v-input__slot {
  background-color: white;
}
</style>
<template>
  <div>
    <v-card elevation="1" class="mt-3">
      <v-card-title>
        <b><v-icon>mdi-calendar-month</v-icon> นัดวันนี้ &nbsp;</b>
        <v-chip color="error" label outlined>
          ทั้งหมด {{ desserts.length }} รายการ
        </v-chip>
        <v-spacer />
        <div class="text-center">
          <v-btn text color="success" @click="fn_addNut">
            <v-icon>mdi-calendar-plus-outline</v-icon> เพิ่มนัด
          </v-btn>
        </div>
      </v-card-title>
      <v-divider />
      <v-card-text>
        <v-card color="rgb(237 235 215)" elevation="0" class="pa-5 mb-3">
          <v-row>
            <v-col md="7" sm="12" cols="12">
              <v-text-field
                class="costomgray"
                prepend-inner-icon="mdi-magnify"
                outlined
                dense
                placeholder="รหัสลูกค้า ชื่อ-นามสกุล เบอร์โทรศัพท์  หรือ แพทย์"
                hide-details
                v-model="formSearch.textSearch"
              ></v-text-field>
            </v-col>
            <v-col md="2" sm="12" cols="12">
              <v-menu
                v-model="menuStart"
                :close-on-content-click="false"
                :nudge-right="40"
                transition="scale-transition"
                offset-y
                min-width="auto"
              >
                <template v-slot:activator="{ on, attrs }">
                  <v-text-field
                    v-model="formSearch.dateStart"
                    label="ตั้งแต่วันที่"
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
                  v-model="formSearch.dateStart"
                  color="deep-orange"
                  @input="menuStart = false"
                ></v-date-picker>
              </v-menu>
            </v-col>
            <v-col md="2" sm="12" cols="12">
              <v-menu
                v-model="menuEnd"
                :close-on-content-click="false"
                :nudge-right="40"
                transition="scale-transition"
                offset-y
                min-width="auto"
              >
                <template v-slot:activator="{ on, attrs }">
                  <v-text-field
                    v-model="formSearch.dateEnd"
                    label="ถึงวันที่"
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
                  v-model="formSearch.dateEnd"
                  color="deep-orange"
                  @input="menuEnd = false"
                ></v-date-picker>
              </v-menu>
            </v-col>
            <v-col md="1" sm="6" cols="12">
              <v-btn elevation="0" color="primary" @click="fn_getData()">
                <v-icon>mdi-magnify</v-icon>
                ค้นหา
              </v-btn>
            </v-col>
          </v-row>
        </v-card>
        <v-divider style="border-top: 1px dashed rgba(0, 0, 0, 0.12)" />
        <v-simple-table fixed-header height="70vh">
          <template v-slot:default>
            <thead>
              <tr>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                  nowrap
                >
                  ลำดับ
                </th>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                  nowrap
                >
                  รหัสลูกค้า
                </th>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                  nowrap
                >
                  ชื่อ-นามสกุล
                </th>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                  nowrap
                >
                  เบอร์โทรศัพท์
                </th>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                  nowrap
                >
                  แพทย์
                </th>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                  nowrap
                >
                  สถานะ
                </th>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                  nowrap
                >
                  เวลานัด
                </th>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                  nowrap
                >
                  เลื่อน/ยกเลิก
                </th>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                  nowrap
                >
                  มาถึง
                </th>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                  nowrap
                >
                  เข้าตรวจ
                </th>
                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                  nowrap
                >
                  ตรวจเสร็จ
                </th>

                <th
                  style="background-color: #212121"
                  class="text-center font-weight-bold white--text"
                  nowrap
                >
                  หมายเหตุ
                </th>
              </tr>
            </thead>
            <tbody>
              <tr
                v-for="(item, i) in desserts"
                :key="i"
                :style="`background-color:${
                  item.Date_finish ? '#E8F5E9' : item.Date_come ? '#FFF3E0' : ''
                };`"
              >
                <td nowrap class="text-center">{{ i + 1 }}</td>
                <td nowrap class="text-left">{{ item.Customer_ID_Show }}</td>
                <td nowrap class="text-left">
                  <v-btn
                    icon
                    :to="`/customer/profile?type=detail&idcus=${item.ID_customer}`"
                  >
                    <v-avatar size="30" v-if="item.img_name">
                      <img
                        :src="`http://localhost/api-myClinic/${item.img_name}`"
                        alt="John"
                      />
                    </v-avatar>
                    <v-avatar size="30" v-else>
                      <v-icon> mdi-account-circle </v-icon>
                    </v-avatar>
                  </v-btn>
                  <span class="pl-3">{{ item.C_Name }}</span>
                </td>
                <td nowrap class="text-left">{{ item.tell }}</td>
                <td nowrap class="text-left">{{ item.D_Name }}</td>
                <td nowrap class="text-center">
                  <v-chip
                    dark
                    :color="
                      item.Status_nut == 'มาถึง' ||
                      item.Status_nut == 'เข้าตรวจ'
                        ? 'warning'
                        : item.Status_nut == 'ตรวจเสร็จ'
                        ? 'success'
                        : item.Status_nut == 'เลื่อนนัด'
                        ? 'purple'
                        : item.Status_nut == 'ยกเลิก'
                        ? 'error'
                        : 'cyan'
                    "
                    small
                    >{{ item.Status_nut }}</v-chip
                  >
                </td>
                <td nowrap class="text-center">
                  {{ DateFormat(item.Date_nut, "DD-MM-YYYY") }}
                  <span style="color: grey">
                    {{ DateFormat(item.Date_nut, "HH:mm A") }}</span
                  >
                </td>
                <td nowrap class="text-center">
                  <v-btn
                    :disabled="
                      !(
                        item.Status_nut == 'นัดหมาย' ||
                        item.Status_nut == 'เลื่อนนัด'
                      )
                    "
                    elevation="0"
                    color="error"
                    small
                    text
                    @click="
                      FormEdit.ID_nut = item.ID_nut;
                      dialogEdit = true;
                    "
                  >
                    <v-icon>mdi-text-box-edit</v-icon>
                  </v-btn>
                </td>
                <td nowrap class="text-center">
                  <span v-if="item.Date_come">{{
                    DateFormat(item.Date_come, "HH:mm A")
                  }}</span>
                  <v-btn
                    v-else-if="item.Status_nut != 'ยกเลิก'"
                    elevation="0"
                    color="info"
                    small
                    @click="fn_CustomerCome(item)"
                  >
                    มาถึง
                  </v-btn>
                </td>
                <td nowrap class="text-center">
                  <v-btn
                    v-if="item.Date_come && !item.Date_inspect"
                    elevation="0"
                    color="warning"
                    small
                    @click="fn_CustomerInspect(item)"
                  >
                    เข้าตรวจ
                  </v-btn>
                  <span v-else>{{
                    DateFormat(item.Date_inspect, "HH:mm A")
                  }}</span>
                </td>
                <td nowrap class="text-center">
                  <v-btn
                    v-if="
                      item.Date_come && item.Date_inspect && !item.Date_finish
                    "
                    elevation="0"
                    color="success"
                    small
                    @click="fn_CustomerFinish(item)"
                  >
                    ตรวจเสร็จ
                  </v-btn>
                  <span v-else>{{
                    DateFormat(item.Date_finish, "HH:mm A")
                  }}</span>
                </td>
                <td nowrap class="text-left">{{ item.Remark }}</td>
              </tr>
            </tbody>
          </template>
        </v-simple-table>
      </v-card-text>
    </v-card>
    <DailogNut ref="dailognut" @getdata="fn_getData"></DailogNut>

    <v-dialog v-model="dialogEdit" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="text-h5"
            ><v-icon>mdi-calendar-plus-outline</v-icon> เลื่อน/ยกเลิก</span
          >
        </v-card-title>
        <v-card-text>
          <v-form ref="forms" lazy-validation>
            <v-row>
              <v-col md="6" sm="12" cols="12">
                <v-select
                  outlined
                  dense
                  hide-details
                  label="เลือก"
                  :items="['เลื่อนนัด', 'ยกเลิก']"
                  v-model="FormEdit.Status_nut"
                  :rules="[(v) => !!v || '']"
                  required
                ></v-select>
              </v-col>
            </v-row>
            <v-row>
              <v-col md="6" sm="12" cols="12">
                <v-menu
                  v-model="menuEdit"
                  :close-on-content-click="false"
                  :nudge-right="40"
                  transition="scale-transition"
                  offset-y
                  min-width="auto"
                >
                  <template v-slot:activator="{ on, attrs }">
                    <v-text-field
                      v-if="FormEdit.Status_nut == 'ยกเลิก'"
                      v-model="FormEdit.Date_nut"
                      label="วันที่"
                      prepend-inner-icon="mdi-calendar"
                      v-bind="attrs"
                      v-on="on"
                      outlined
                      dense
                      hide-details
                      :disabled="FormEdit.Status_nut == 'ยกเลิก'"
                      class="costomgray"
                    ></v-text-field>
                    <v-text-field
                      v-else
                      v-model="FormEdit.Date_nut"
                      label="วันที่"
                      prepend-inner-icon="mdi-calendar"
                      readonly
                      v-bind="attrs"
                      v-on="on"
                      outlined
                      dense
                      hide-details
                      :disabled="FormEdit.Status_nut == 'ยกเลิก'"
                      :rules="[(v) => !!v || '']"
                      required
                      class="costomgray"
                    ></v-text-field>
                  </template>
                  <v-date-picker
                    v-model="FormEdit.Date_nut"
                    :min="currentDate"
                    color="deep-orange"
                    @input="menuEdit = false"
                  ></v-date-picker>
                </v-menu>
              </v-col>
              <v-col md="6" sm="12" cols="12">
                <v-text-field
                  v-if="FormEdit.Status_nut == 'ยกเลิก'"
                  label="เวลานัด"
                  outlined
                  dense
                  :disabled="FormEdit.Status_nut == 'ยกเลิก'"
                  hide-details
                  type="time"
                  v-model="FormEdit.start_time"
                ></v-text-field>
                <v-text-field
                  v-else
                  label="เวลานัด"
                  outlined
                  dense
                  :disabled="FormEdit.Status_nut == 'ยกเลิก'"
                  hide-details
                  type="time"
                  v-model="FormEdit.start_time"
                  :rules="[(v) => !!v || '']"
                  required
                ></v-text-field>
              </v-col>
            </v-row>
            <v-row class="pb-3">
              <!-- <v-col md="6" sm="12" cols="12">
                <v-text-field
                  v-if="FormEdit.Status_nut == 'ยกเลิก'"
                  label="เริ่มเวลา"
                  outlined
                  dense
                  hide-details
                  type="time"
                  v-model="FormEdit.start_time"
                ></v-text-field>
                <v-text-field
                  v-else
                  label="เวลานัด"
                  outlined
                  dense
                  hide-details
                  type="time"
                  v-model="FormEdit.start_time"
                  :rules="[(v) => !!v || '']"
                  required
                ></v-text-field>
              </v-col>
              <v-col md="6" sm="12" cols="12">
                <v-text-field
                  v-if="FormEdit.Status_nut == 'ยกเลิก'"
                  label="สิ้นสุด"
                  outlined
                  dense
                  hide-details
                  type="time"
                  v-model="FormEdit.end_time"
                ></v-text-field>
                <v-text-field
                  v-else
                  label="สิ้นสุด"
                  outlined
                  dense
                  hide-details
                  type="time"
                  v-model="FormEdit.end_time"
                  :rules="[(v) => !!v || '']"
                  required
                ></v-text-field>
              </v-col> -->
              <v-col md="12" sm="12" cols="12">
                <v-textarea
                  v-if="FormEdit.Status_nut == 'ยกเลิก'"
                  outlined
                  dense
                  hide-details
                  label="หมายเหตุ"
                  rows="3"
                  v-model="FormEdit.Remark"
                  :rules="[(v) => !!v || '']"
                  required
                ></v-textarea>
                <v-textarea
                  v-else
                  outlined
                  dense
                  hide-details
                  label="หมายเหตุ"
                  rows="3"
                  v-model="FormEdit.Remark"
                ></v-textarea>
              </v-col>
            </v-row>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="blue darken-1"
            text
            @click="
              $refs.forms.reset();
              dialogEdit = false;
            "
          >
            Close
          </v-btn>
          <v-btn color="blue darken-1" text @click="fn_updateNut"> Save </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogCost" width="700">
      <v-card>
        <v-card-title class="text-h5"> ค่าใช้จ่าย </v-card-title>

        <v-card-text>
          <v-divider style="border-top: 1px dashed rgba(0, 0, 0, 0.12)" />
          <v-simple-table>
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
                    รายการ
                  </th>
                  <th
                    style="background-color: #212121"
                    class="text-center font-weight-bold white--text"
                  >
                    ราคา
                  </th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(item, i) in expenses" :key="i">
                  <td nowrap class="text-center">{{ i + 1 }}</td>
                  <td nowrap class="text-left">{{ item.treat_name }}</td>
                  <td nowrap class="text-right">
                    {{ numberFormat(item.treat_price, 2) }}
                  </td>
                </tr>
                <tr>
                  <td colspan="2" class="text-right">รวม</td>
                  <td colspan="2" class="text-right">
                    {{
                      numberFormat(
                        expenses.reduce(
                          (a, b) => a + parseFloat(b.treat_price),
                          0
                        ),
                        2
                      )
                    }}
                  </td>
                </tr>
              </tbody>
            </template>
          </v-simple-table>
        </v-card-text>

        <v-divider></v-divider>

        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="primary" text @click="fn_save_expenses(myItem)">
            ชำระเงิน
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <ConfirmDlg ref="confirm" />
  </div>
</template>
<script>
import axios from "axios";
import Vmenu from "@/components/Vmenu";
import DailogNut from "@/components/Sub/DailogNut";
import ConfirmDlg from "@/components/ConfirmDlg.vue";
export default {
  name: "IndexPage",
  components: {
    Vmenu,
    DailogNut,
    ConfirmDlg,
  },
  data() {
    return {
      menuDailog: false,
      menuStart: false,
      menuEnd: false,
      menuEdit: false,
      dialog: false,
      dialogEdit: false,
      dialogCost: false,
      currentDate: new Date(Date.now() - new Date().getTimezoneOffset() * 60000)
        .toISOString()
        .substr(0, 10),
      desserts: [],
      FormEdit: {
        Date_nut: new Date(Date.now() - new Date().getTimezoneOffset() * 60000)
          .toISOString()
          .substr(0, 10),
        Status_nut: "",
        start_time: "",
        end_time: "",
        Remark: "",
        ID_nut: "",
      },
      textSearch: "",
      formSearch: {
        textSearch: "",
        dateStart: new Date(Date.now() - new Date().getTimezoneOffset() * 60000)
          .toISOString()
          .substr(0, 10),
        dateEnd: new Date(Date.now() - new Date().getTimezoneOffset() * 60000)
          .toISOString()
          .substr(0, 10),
      },
      expenses: [],
      myItem:{}
    };
  },
  methods: {
    async fn_getData() {
      await axios
        .get(`${process.env.api_url}/Appointment_con/get_appointment`, {
          params: this.formSearch,
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
    fn_addNut() {
      this.$refs.dailognut.open();
    },

    async fn_CustomerCome(item) {
      let dateNow = new Date(
        Date.now() - new Date().getTimezoneOffset() * 60000
      ).toISOString();
      item.Date_come = `${dateNow.substring(0,10)} ${dateNow.substring(11,16)}` ;
      item.Status_nut = "มาถึง";
      let data = JSON.stringify({
        Date_come: item.Date_come,
        Status_nut: item.Status_nut,
        ID_nut: item.ID_nut,
      });
      await axios
        .post(`${process.env.api_url}/appointment/update`, data, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.dialog = false;
          this.$emit("getdata");
        })
        .catch((err) => {
          alert(err);
        });
      // alert(item)
    },
    async fn_CustomerInspect(item) {
      let dateNow = new Date(
        Date.now() - new Date().getTimezoneOffset() * 60000
      ).toISOString();
      item.Date_inspect = `${dateNow.substring(0,10)} ${dateNow.substring(11,16)}` ;
      item.Status_nut = "เข้าตรวจ";
      let data = JSON.stringify({
        Date_inspect: item.Date_inspect,
        Status_nut: item.Status_nut,
        ID_nut: item.ID_nut,
      });
      await axios
        .post(`${process.env.api_url}/appointment/update`, data, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.dialog = false;
          this.$emit("getdata");
        })
        .catch((err) => {
          alert(err);
        });
      // alert(item)
    },
    // async fn_CustomerFinish(item) {
    //   this.myItem =item;
    //   this.dialogCost = true;
    //   await axios
    //     .get(`${process.env.api_url}/appointment/expenses`, {
    //       params: item,
    //       headers: {
    //         "Content-Type": "application/json",
    //       },
    //     })
    //     .then((res) => {
    //       this.expenses = res.data.data;
    //     })
    //     .catch((err) => {
    //       alert(err);
    //     });
      
    // },
    async fn_CustomerFinish(item) {
     let dateNow = new Date(
        Date.now() - new Date().getTimezoneOffset() * 60000
      ).toISOString();
      item.Date_finish = `${dateNow.substring(0,10)} ${dateNow.substring(11,16)}` ;
      item.Status_nut = "ตรวจเสร็จ";
      let data = JSON.stringify({
        Date_finish: item.Date_finish,
        Status_nut: item.Status_nut,
        ID_nut: item.ID_nut,
      });
      await axios
        .post(`${process.env.api_url}/appointment/update`, data, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.dialog = false;
          this.$emit("getdata");
        })
        .catch((err) => {
          alert(err);
        });
      
    },
    // async fn_save_expenses(item) {
    //   this.dialogCost = false;
    //   item.Date_finish = new Date(
    //     Date.now() - new Date().getTimezoneOffset() * 60000
    //   ).toISOString();
    //   item.Status_nut = "ตรวจเสร็จ";
    //   let data = JSON.stringify({
    //     Date_finish: item.Date_come,
    //     Status_nut: item.Status_nut,
    //     ID_nut: item.ID_nut,
    //   });
    //   await axios
    //     .post(`${process.env.api_url}/appointment/update`, data, {
    //       headers: {
    //         "Content-Type": "application/json",
    //       },
    //     })
    //     .then((res) => {
    //       this.dialog = false;
    //       this.$emit("getdata");
    //     })
    //     .catch((err) => {
    //       alert(err);
    //     });
    // },
    async fn_updateNut() {
      if (!this.$refs.forms.validate()) {
        this.$refs.confirm.dailogalert("กรุณากรอกข้อมูล", ``, {
          icon: "error",
          color: "error",
          btnCanceltext: "ตกลง",
        });
        return false;
      }
      let curDate = new Date(Date.now());
      let Date_nut = new Date(
        `${this.FormEdit.Date_nut} ${this.FormEdit.start_time}`
      );
      if (Date_nut < curDate && this.FormEdit.Status_nut != "ยกเลิก") {
        this.$refs.confirm.dailogalert("ไม่สามารถนัดย้อนหลัง", ``, {
          icon: "error",
          color: "error",
          btnCanceltext: "ตกลง",
        });
        return false;
      }
      let data = JSON.stringify(this.FormEdit);
      await axios
        .post(`${process.env.api_url}/appointment/update`, data, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.$refs.confirm.dailogalert(
            this.FormEdit.Status_nut + " สำเร็จ",
            ``,
            {
              icon: "success",
              color: "success",
              btnCanceltext: "ตกลง",
            }
          );
          this.dialogEdit = false;
          this.$refs.forms.reset();
          this.fn_getData();
        })
        .catch((err) => {
          alert(err);
        });
    },
  },
  mounted() {
    try {
      if (this.$route.query.Date) {
        this.formSearch.dateEnd = this.$route.query.Date;
        this.formSearch.dateStart = this.$route.query.Date;
      }
      this.fn_getData();
    } catch {}
  },
};
</script>