<template>
  <div>
    <v-list two-line>
      <v-list-item-group active-class="pink--text">
        <template v-for="(item, index) in groupTreatment">
          <v-list-item :key="item.title" @click="fn_detaailTreat(item)">
            <template v-slot:default="{ active }">
              <v-list-item-icon>
                {{ index + 1 }}
              </v-list-item-icon>
              <v-list-item-content>
                <v-list-item-title v-text="item.treat_name"></v-list-item-title>
              </v-list-item-content>
              <v-list-item-action>
                <v-list-item-action-text>{{
                  item.Amount
                }}</v-list-item-action-text>
              </v-list-item-action>
            </template>
          </v-list-item>

          <v-divider
            v-if="index < groupTreatment.length - 1"
            :key="index"
          ></v-divider>
        </template>
      </v-list-item-group>
    </v-list>
    <v-dialog
      v-model="dialog"
      fullscreen
      hide-overlay
      transition="dialog-bottom-transition"
    >
      <v-card>
        <v-toolbar dark color="#212121">
          <v-toolbar-title
            >{{ myProfile.ID_customer }} - {{ myProfile.Name }}</v-toolbar-title
          >
          <v-spacer></v-spacer>
          <v-btn color="#D4AF37" @click="dialog_insert = true">
            <v-icon>mdi-book-plus-multiple</v-icon> เพิ่มประวัติการรักษา
          </v-btn>
          <v-btn icon dark @click="dialog = false">
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-toolbar>
        <br />
        <v-row>
          <v-col md="5" sm="12" cols="12">
            <div style="height: 85vh; overflow: auto">
              <!-- <div class="">
                <v-btn text color="success" @click="dialog_insert = true">
                  <v-icon>mdi-book-plus-multiple</v-icon> เพิ่มประวัติการรักษา
                </v-btn>
              </div> -->
              <v-list two-line>
                <v-list-item-group v-model="selectHe" active-class="pink--text">
                  <template v-for="(item, index) in Treatment">
                    <v-list-item
                      :key="item.title"
                      @click="fn_detaailTreat2(item)"
                    >
                      <template v-slot:default="{ active }">
                        <v-list-item-icon>
                          {{ index + 1 }}
                        </v-list-item-icon>
                        <v-list-item-content>
                          <v-list-item-title
                            >{{ item.treat_name }}
                          </v-list-item-title>
                          <spa
                            style="font-size: 0.875rem; color: rgb(0 0 0 / 87%)"
                            >{{ item.treatmens_detail }}</spa
                          >
                        </v-list-item-content>
                        <v-list-item-action>
                          <v-list-item-action-text
                            v-text="item.Date_save"
                          ></v-list-item-action-text>
                          <!-- <div class="">
                            <v-btn text color="success" @click="dialog_img = true; Treatment_item = item">
                              <v-icon>mdi-image-edit</v-icon> เพิ่มรูป
                            </v-btn>
                          </div> -->
                        </v-list-item-action>
                      </template>
                    </v-list-item>
                    <v-divider
                      v-if="index < Treatment.length - 1"
                      :key="index"
                    ></v-divider>
                  </template>
                </v-list-item-group>
              </v-list>
            </div>
          </v-col>
          <v-col md="7" sm="12" cols="12">
            <div v-if="get_img != ''" tyle="height: 85vh; overflow: auto">
              <v-row>
                <v-col v-for="n in get_img" :key="n" cols="12">
                  <v-img
                    :src="`http://localhost/api-myClinic/${n.filepath}`"
                    :aspect-ratio="16 / 8"
                    class="grey lighten-2"
                  >
                    <template v-slot:placeholder>
                      <v-row
                        class="fill-height ma-0"
                        align="center"
                        justify="center"
                      >
                        <v-progress-circular
                          indeterminate
                          color="grey lighten-5"
                        ></v-progress-circular>
                      </v-row>
                    </template>
                  </v-img>
                </v-col>
              </v-row>
            </div>
          </v-col>
        </v-row>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialog_insert" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="text-h5"
            ><v-icon>mdi-calendar-plus-outline</v-icon>
            เพิ่มประวัติการรักษา</span
          >
        </v-card-title>
        <v-card-text>
          <v-form ref="form" lazy-validation>
            <v-row>
              <v-col md="12" sm="12" cols="12">
                <v-textarea
                  outlined
                  label="รายละเอียดการรักษา"
                  v-model="treatmens_detail"
                  :rules="[(v) => !!v || '']"
                  required
                ></v-textarea>
              </v-col>
            </v-row>
            <v-row class="mt-2">
              <v-col md="12" sm="12" cols="12">
                <v-file-input
                  multiple
                  v-model="Img"
                  label="ภาพประกอบ"
                  outlined
                  dense
                ></v-file-input>
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
              dialog_insert = false;
              treatmens_detail = '';
            "
          >
            Close
          </v-btn>
          <v-btn color="blue darken-1" text @click="fn_insert"> Save </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialog_img" persistent max-width="600px">
      <v-card>
        <v-card-title>
          <span class="text-h5"
            ><v-icon>mdi-calendar-plus-outline</v-icon>
            เพิ่มรูปประวัติการรักษา</span
          >
        </v-card-title>
        <v-card-text>
          <v-row class="mt-2">
            <v-col md="12" sm="12" cols="12">
              <v-file-input
                multiple
                v-model="Img"
                label="ภาพประกอบ"
                outlined
                dense
              ></v-file-input>
            </v-col>
          </v-row>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn
            color="blue darken-1"
            text
            @click="
              dialog_img = false;
              Img = [];
            "
          >
            Close
          </v-btn>
          <v-btn color="blue darken-1" text @click="fn_inserimg"> Save </v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <ConfirmDlg ref="confirm" />
  </div>
</template>
<script>
import axios from "axios";
import ConfirmDlg from "@/components/ConfirmDlg.vue";
export default {
  props: {
    myProfile: Object,
  },
  components: {
    ConfirmDlg,
  },
  data() {
    return {
      dialog_img: false,
      Img: [],
      ID_nut: 0,
      groupTreatment: [],
      Treatment: [],
      dialog: false,
      detaailHe: {},
      selectHe: null,
      Treatment_item: {},
      get_img: [],
      dialog_insert: false,
      treatmens_detail: "",
      ID_treat: 0,
    };
  },
  methods: {
    async fn_insert() {
      if (!this.$refs.form.validate()) {
        this.$refs.confirm.dailogalert("กรุณากรอกข้อมูล", ``, {
          icon: "error",
          color: "error",
          btnCanceltext: "ตกลง",
        });
        return false;
      }
      let formData = new FormData();
      formData.append("treatmens_detail", this.treatmens_detail);
      formData.append("ID_customer", this.$route.query.idcus);
      formData.append("ID_pagekage_treat", this.ID_treat);
      this.Img.forEach((element, index) => {
        formData.append("Img" + index, element);
      });
      formData.append("index", this.Img.length);
      await axios
        .post(
          `${process.env.api_url}/Treatment_con/insert_treatmens`,
          formData,
          {
            headers: {
              "Content-Type": "multipart/form-data",
            },
          }
        )
        .then(async (res) => {
          this.$refs.confirm.dailogalert("เพิ่มข้อมูลเรียบร้อย", ``, {
            icon: "success",
            color: "success",
            btnCanceltext: "ตกลง",
          });
          this.dialog_insert = false;
          this.fn_getData(this.$route.query.idcus);
          await axios
            .get(
              `${process.env.api_url}/treatment?IDCus=${this.$route.query.idcus}&ID_treat=${this.ID_treat}`,
              {
                headers: {
                  "Content-Type": "application/json",
                },
              }
            )
            .then((res) => {
              this.Treatment = res.data.data;
            })
            .catch((err) => {
              alert(err);
            });
          (this.treatmens_detail = ""), (this.Img = []);
          this.dialog = true;
        })
        .catch((err) => {
          alert(err);
        });
    },
    async fn_inserimg() {
      let formData = new FormData();

      formData.append("ID_customer", this.Treatment_item.ID_customer);
      formData.append("ID_package", this.Treatment_item.ID_package);
      formData.append("ID_nut", this.Treatment_item.ID_nut);
      await axios
        .post(`${process.env.api_url}/ConfigCon/insert_doctor`, formData, {
          headers: {
            "Content-Type": "multipart/form-data",
          },
        })
        .then((res) => {
          this.dialog_img = false;
          this.fn_getData();
        })
        .catch((err) => {
          alert(err);
        });
    },
    async fn_getData(idcus) {
      await axios
        .get(`${process.env.api_url}/treatment/group?IDCus=${idcus}`, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.groupTreatment = res.data.data;
        })
        .catch((err) => {
          alert(err);
        });
    },
    async fn_detaailTreat(item) {
      this.ID_treat = item.ID_treat;
      await axios
        .get(
          `${process.env.api_url}/treatment?IDCus=${this.$route.query.idcus}&ID_treat=${item.ID_treat}`,
          {
            headers: {
              "Content-Type": "application/json",
            },
          }
        )
        .then(async (res) => {
          this.Treatment = res.data.data;
          await axios
            .get(
              `${process.env.api_url}/Treatment_con/get_img?id_customer=${this.$route.query.idcus}&id_type=${item.ID_treat}&id_rendezvous=`,
              {
                headers: {
                  "Content-Type": "application/json",
                },
              }
            )
            .then((res) => {
              this.get_img = res.data.data;
            });
        })
        .catch((err) => {
          alert(err);
        });

      this.dialog = true;
    },
    async fn_detaailTreat2(item) {
      await axios
        .get(
          `${process.env.api_url}/Treatment_con/get_img?id_customer=${this.$route.query.idcus}&id_type=${item.ID_treat}&id_rendezvous=${item.ID_treatments}`,
          {
            headers: {
              "Content-Type": "application/json",
            },
          }
        )
        .then((res) => {
          this.get_img = res.data.data;
          console.log(res.data.data);
        })
        .catch((err) => {
          alert(err);
        });
      // this.detaailHe = item;
    },
  },
  async mounted() {
    if (this.$route.query.idcus) {
      await this.fn_getData(this.$route.query.idcus);
    }
  },
};
</script>
