<style scoped>
::v-deep .costomgray>.v-input__control>.v-input__slot {
  background-color: white;
}
</style>
<template>
  <div>
    <v-card elevation="1" class="mt-3">
      <v-card-title>
        <b><v-icon>mdi-doctor</v-icon> แพทย์ &nbsp;</b>
        <v-chip color="error" label outlined>
          ทั้งหมด {{ desserts.length }} รายการ
        </v-chip>
        <v-spacer />
        <div class="text-center">
          <v-btn text color="success" @click="
            dialog = true;
          type = 1;
          formInsert.ID_Doctor = '';
          formInsert.Fisrtname = '';
          formInsert.Lastname = '';
          formInsert.License = '';
          formInsert.ID = '';
          formInsert.Status = '';
          ">
            <v-icon>mdi-calendar-plus-outline</v-icon> เพิ่มแพทย์
          </v-btn>
        </div>
      </v-card-title>
      <v-divider />
      <v-card-text>
        <v-card color="rgb(237 235 215)" elevation="0" class="pa-5 mb-3">
          <v-row>
            <v-col md="11" sm="12" cols="12">
              <v-text-field class="costomgray" prepend-inner-icon="mdi-magnify" outlined dense
                placeholder="รหัสแพทย์ หรือ ชื่อ-นามสกุล" hide-details v-model="textSearch"></v-text-field>
            </v-col>
            <v-col md="1" sm="12" cols="12">
              <v-btn elevation="0" color="primary" @click="fn_getData">
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
                <th style="background-color: #212121" class="text-center font-weight-bold white--text">
                  ลำดับ
                </th>
                <th style="background-color: #212121" class="text-left font-weight-bold white--text">
                  รหัสแพทย์
                </th>
                <th style="background-color: #212121" class="text-left font-weight-bold white--text">
                  ชื่อ-นามสกุล
                </th>
                <th style="background-color: #212121" class="text-left font-weight-bold white--text">
                  ใบประกอบ
                </th>
                <th style="background-color: #212121" class="text-left font-weight-bold white--text">
                  สถานะ
                </th>
                <th style="background-color: #212121" class="text-left font-weight-bold white--text">
                  Action
                </th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="(item, i) in desserts" :key="i">
                <td class="text-center">{{ i + 1 }}</td>
                <td class="text-left">{{ item.ID_Doctor }}</td>
                <td class="text-left">
                  <v-avatar size="30">
                    <v-icon> mdi-account-circle </v-icon>
                  </v-avatar>
                  <span class="pl-3">{{ item.Fisrtname }}&nbsp;{{ item.Lastname }}</span>
                </td>
                <td class="text-left">
                  <v-btn v-if="item.License" elevation="0" color="primary" text small
                    @click="get_imgdoctor(item.License)">
                    <v-icon>mdi-image</v-icon>
                  </v-btn>
                </td>
                <td class="text-left">
                  <v-switch dense @change="updateStatus(!item.Status, item.ID)"
                    :label="item.Status ? `เปิดใช้งาน` : `ปิดใช้งาน`" v-model="item.Status" color="#D4AF37" value
                    inset></v-switch>
                </td>
                <td class="text-left">
                  <v-btn elevation="0" color="warning" x-small text @click="Edtil_doctor(item.ID)">
                    <v-icon> mdi-text-box-edit</v-icon>
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
          <span class="text-h5" v-if="type == 1"><v-icon>mdi-calendar-plus-outline</v-icon> เพิ่มแพทย์</span>
          <span class="text-h5" v-else><v-icon>mdi-calendar-plus-outline</v-icon> แก้ไขแพทย์</span>
        </v-card-title>
        <v-card-text>
          <v-form ref="form" lazy-validation>
            <v-row>
              <v-col md="6" sm="12" cols="12">
                <v-text-field label="รหัส" outlined dense hide-details v-model="formInsert.ID_Doctor"
                  :rules="[(v) => !!v || '']" required></v-text-field>
              </v-col>
            </v-row>
            <v-row>
              <v-col md="6" sm="12" cols="12">
                <v-text-field label="ชื่อ" outlined dense hide-details :rules="[(v) => !!v || '']" required
                  v-model="formInsert.Fisrtname"></v-text-field>
              </v-col>
              <v-col md="6" sm="12" cols="12">
                <v-text-field label="นามสกุล" outlined dense hide-details :rules="[(v) => !!v || '']" required
                  v-model="formInsert.Lastname"></v-text-field>
              </v-col>
            </v-row>
          </v-form>
          <v-row class="mt-2" v-if="type == 1">
            <v-col md="12" sm="12" cols="12">
              <v-file-input multiple v-model="formInsert.License" label="ใบประกอบ" outlined dense></v-file-input>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" text @click="dialog = false">
            Close
          </v-btn>
          <v-btn color="blue darken-1" text @click="fn_insertData" v-if="type == 1">
            Save
          </v-btn>
          <v-btn color="blue darken-1" text @click="fn_EdtilData()" v-if="type == 2">
            Save Edtil
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialog_img" persistent max-width="600px">
      <template>
        <v-card>
          <v-toolbar dark color="#212121">
            <v-toolbar-title>รูปใบประกอบ</v-toolbar-title>
            <v-spacer></v-spacer>
            <v-btn icon dark @click="dialog_img = false">
              <v-icon>mdi-close</v-icon>
            </v-btn>
          </v-toolbar>
          <v-card-text>
            <v-carousel v-if="items">
              <v-carousel-item :src="`http://191.191.209.51/api-myClinic/${items}`" reverse-transition="fade-transition"
                transition="fade-transition"></v-carousel-item>
            </v-carousel>
            <span v-else>ไม่มีภาพ</span>
          </v-card-text>
        </v-card>
      </template>
    </v-dialog>
    <ConfirmDlg ref="confirm" />
  </div>
</template>
<script>
import axios from "axios";
import Vmenu from "@/components/Vmenu";
import ConfirmDlg from "@/components/ConfirmDlg.vue";
export default {
  name: "IndexPage",
  components: {
    Vmenu,
    ConfirmDlg,
  },
  data() {
    return {
      dialog: false,
      desserts: [],
      textSearch: "",
      formInsert: {
        ID_Doctor: "",
        Fisrtname: "",
        Lastname: "",
        License: [],
        ID: "",
        Status: "",
      },
      type: 1,
      items: '',
      dialog_img: false,
    };
  },
  methods: {
    async get_imgdoctor(License) {
      this.dialog_img = true;
      this.items = License;
    },
    async Edtil_doctor(ID) {
      await axios
        .get(`${process.env.api_url}/doctor?ID=${ID}&textSearch=&status=`, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.formInsert = res.data.data[0];
          this.type = 2;
          this.dialog = true;
        })
        .catch((err) => {
          alert(err);
        });
    },
    async fn_getData() {
      await axios
        .get(
          `${process.env.api_url}/doctor?ID=&textSearch=${this.textSearch}&status=`,
          {
            headers: {
              "Content-Type": "application/json",
            },
          }
        )
        .then((res) => {
          this.desserts = res.data.data;
        })
        .catch((err) => {
          alert(err);
        });
    },
    async fn_insertData() {
      if (!this.$refs.form.validate()) {
        this.$refs.confirm.dailogalert("กรุณากรอกข้อมูล", ``, {
          icon: "error",
          color: "error",
          btnCanceltext: "ตกลง",
        });
        return false;
      }
      let formData = new FormData();

      this.formInsert.License.forEach((element, index) => {
        formData.append("fileLicense" + index, element);
      });
      formData.append("index", this.formInsert.License.length);
      formData.append("ID_Doctor", this.formInsert.ID_Doctor);
      formData.append("Fisrtname", this.formInsert.Fisrtname);
      formData.append("Lastname", this.formInsert.Lastname);
      await axios
        .post(`${process.env.api_url}/ConfigCon/insert_doctor`, formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        })
        .then((res) => {
          this.$refs.confirm.dailogalert("เพิ่มข้อมูลเรียบร้อย", ``, {
            icon: "success",
            color: "success",
            btnCanceltext: "ตกลง",
          });
          this.dialog = false;
          this.fn_getData();
        })
        .catch((err) => {
          alert(err);
        });
    },
    async fn_EdtilData() {
      if (!this.$refs.form.validate()) {
        this.$refs.confirm.dailogalert("กรุณากรอกข้อมูล", ``, {
          icon: "error",
          color: "error",
          btnCanceltext: "ตกลง",
        });
        return false;
      }
      if (this.$refs.form.validate()) {
        let data = JSON.stringify(this.formInsert);
        await axios
          .post(`${process.env.api_url}/ConfigCon/update_doctor`, data, {
            headers: {
              "Content-Type": "multipart/form-data",
            },
          })
          .then((res) => {
            this.$refs.confirm.dailogalert("เเก้ไขข้อมูลเรียบร้อย", ``, {
              icon: "success",
              color: "success",
              btnCanceltext: "ตกลง",
            });
            this.dialog = false;
            this.fn_getData();
          })
          .catch((err) => {
            alert(err);
          });
      }
    },
    async fn_delateData(ID) {
      let formData = new FormData();
      formData.append("ID", ID);
      await axios
        .post(`${process.env.api_url}/ConfigCon/delete_doctor`, formData, {
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
    async updateStatus(status, id) {
      this.formInsert.ID = id;
      this.formInsert.Status = status ? "0" : "1";
      let data = JSON.stringify(this.formInsert);
      await axios
        .post(`${process.env.api_url}/ConfigCon/update_doctor`, data, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.formInsert.ID = "";
          this.formInsert.Status = "";
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