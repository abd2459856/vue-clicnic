<style scoped>
::v-deep .costomgray>.v-input__control>.v-input__slot {
  background-color: white;
}
</style>
<template>
  <div>
    <v-card elevation="1" class="mt-3">
      <v-card-title>
        <b><v-icon>mdi-account-tie</v-icon> คนไข้&nbsp;</b>
        <v-chip color="error" label outlined>
          ทั้งหมด {{ desserts.length }} รายการ
        </v-chip>
        <v-spacer />
        <div class="text-center">
          <v-btn text color="success" to="/customer/profile?type=add">
            <v-icon>mdi-account-multiple-plus</v-icon> เพิ่มคนไข้
          </v-btn>
        </div>
      </v-card-title>
      <v-divider />
      <v-card-text>
        <v-card color="rgb(237 235 215)" elevation="0" class="pa-5 mb-3">
          <v-row>
            <v-col md="11" sm="12" cols="12">
              <v-text-field class="costomgray" prepend-inner-icon="mdi-magnify" outlined dense
                placeholder="เบอร์โทรศัพท์ ชื่อ-นามสกุล เลขบัตรประชาชน" hide-details v-model="textSearch"></v-text-field>
            </v-col>
            <v-col md="1" sm="12" cols="12">
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
                <th style="background-color: #212121" class="text-center font-weight-bold white--text">
                  ลำดับ
                </th>
                <th style="background-color: #212121" class="text-center font-weight-bold white--text">
                  รหัสลูกค้า
                </th>
                <th style="background-color: #212121" class="text-center font-weight-bold white--text">
                  ชื่อเล่น
                </th>
                <th style="background-color: #212121" class="text-center font-weight-bold white--text">
                  ชื่อ - สกุล
                </th>
                <th style="background-color: #212121" class="text-center font-weight-bold white--text">
                  เบอร์โทรศัพท์
                </th>
                <th style="background-color: #212121" class="text-center font-weight-bold white--text">
                  สถานะ
                </th>
                <th style="background-color: #212121" class="text-center font-weight-bold white--text">
                  Action
                </th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(r, i) in desserts" :key="i">
                <td class="text-center" nowrap>{{ i+1 }}</td>
                <td nowrap class="text-left">{{ r.Customer_ID_Show }}</td>
                <td nowrap class="text-left">{{ r.Nickname }}</td>
                <td nowrap class="text-left">
                  <v-avatar size="30" v-if="r.img_name">
                    <img :src="`http://localhost/api-myClinic/${r.img_name}`" alt="John" />
                  </v-avatar>
                  <v-avatar size="30" v-else>
                    <v-icon > mdi-account-circle </v-icon>
                  </v-avatar>
                  <span class="pl-3">{{ r.Fisrtname }}&nbsp;{{ r.Lastname }}</span>
                </td>
                <td nowrap class="text-left">{{ r.tell }}</td>
                <td nowrap class="d-flex justify-center">
                  <v-switch dense @change="updateStatus(!r.status, r.ID_customer)"
                    :label="r.status == 1 ? `เปิดใช้งาน` : `ปิดใช้งาน`" v-model="r.status" color="#D4AF37" value
                    inset></v-switch>
                </td>
                <td nowrap class="text-center">
                  <v-btn elevation="0" color="info" x-small text
                    :to="`/customer/profile?type=detail&idcus=${r.ID_customer}`">
                    <v-icon> mdi-eye</v-icon>
                  </v-btn>
                  <v-btn
                    elevation="0"
                    color="warning"
                    x-small
                    text
                    :to="`/customer/profile?type=edit&idcus=${r.ID_customer}`"
                  >
                    <v-icon>mdi-text-box-edit</v-icon>
                  </v-btn>
                   <v-btn
                    elevation="0"
                    color="teal lighten-1"
                    x-small
                    text
                    @click="loadExcel(r.ID_customer)"
                  >
                    <v-icon>mdi-file-excel-outline</v-icon>
                  </v-btn>
                  <!-- <v-btn elevation="0" color="error" x-small text @click="fn_delateData(r.ID_customer)">
                    <v-icon> mdi-delete</v-icon>
                  </v-btn> -->
                </td>
              </tr>
            </tbody>
          </template>
        </v-simple-table>
      </v-card-text>
    </v-card>
  </div>
</template>
  
<script>
import axios from "axios";
export default {
  name: "InspirePage",
  data: () => ({
    skeletonshow: true,
    countdata: 0,
    page: 1,
    total_record: 1,
    desserts: [],
    textSearch: '',
  }),
  methods: {
    async fn_getData() {
      await axios
        .get(`${process.env.api_url}/customer?textSearch=${this.textSearch}&IDCus=`, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.desserts = res.data.newrespone;
        })
        .catch((err) => {
          alert(err);
        });
    },
    async updateStatus(status, id) {
      status = status == 1 ? 0 : 1;
      let data = new FormData();
      data.append("ID_customer", id);
      data.append("status", status);
      // this.FormAdd.ID_treat = id;
     
      await axios
        .post(`${process.env.api_url}/customer/update_status`, data, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {

        })
        .catch((err) => {
          alert(err);
        });
    },
    async fn_delateData(ID) {
      let data = JSON.stringify({
        ID: ID,
      });
      await axios
        .post(`${process.env.api_url}/customer/delete`, data, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.fn_getData();
        })
        .catch((err) => {
          alert(err);
        });
    },
    async loadExcel(ID_customer) {
            // window.location.replace(
            //     `${process.env.api_url}Customer_con/Export_Excel?ID_customer=${ID_customer}`
            // );
            window.open(
                `${process.env.api_url}/Customer_con/Export_Excel?ID_customer=${ID_customer}`, "_blank"
            );
        },
  },
  mounted() {
    this.fn_getData()
  }
};
</script>
  