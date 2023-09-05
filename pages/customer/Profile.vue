<template>
  <div>
    <v-card color="#212121" elevation="0" height="300px" class="pa-5">
      <div align="right" class="mb-5">
        <v-btn depressed text color="#D4AF37" @click="fn_addNut()"> เพิ่มนัด </v-btn>
        <!-- <v-btn depressed text color="#D4AF37">
          การรักษา
        </v-btn> -->
      </div>
    </v-card>
    <div class="pl-5 pr-5" style="margin-top: -150px !important">
      <v-row>
        <v-col md="3">
          <div align="center" style="margin-top: -100px !important">
            <v-avatar size="200" style="border: 3px solid #d4af37; z-index: 1">
              <img
                src="https://avatars0.githubusercontent.com/u/9064066?v=4&s=460"
                alt="John"
              />
            </v-avatar>
          </div>
          <v-card elevation="0" style="margin-top: -100px !important">
            <v-card-text style="padding-top: 130px" class="pl-10">
              <h1 align="center" class="font-weight-bold pb-5">{{myProfile.Nickname}}</h1>
              <p>
                <v-icon color="#D4AF37" small>mdi-account</v-icon>&nbsp; {{myProfile.Name}}
              </p>
              <p>
                <v-icon color="#D4AF37" small>mdi-id-card</v-icon>&nbsp;
                {{myProfile.ID_customer}}
              </p>
              <p>
                <v-icon color="#D4AF37" small>mdi-email</v-icon>&nbsp;
               {{ myProfile.email}}
              </p>
              <p>
                <v-icon color="#D4AF37" small>mdi-phone</v-icon>&nbsp;
                {{ myProfile.tell}}
              </p>

              <p>
                <v-icon color="#D4AF37" small>mdi-calendar</v-icon>&nbsp;
                มาครั้งล่าสุด {{ DateFormat(myProfile.Date_nut,'DD-MM-YYYY')}}
              </p>
            </v-card-text>
          </v-card>
        </v-col>
        <v-col md="9">
          <v-card elevation="0">
            <v-tabs
              v-model="tab"
              background-color="rgb(234, 233, 226)"
              color="#D4AF37"
            >
              <v-tab> ประวัติคนไข้ </v-tab>
              <v-tab v-if="$route.query.type == 'detail'"> การรักษา </v-tab>
            </v-tabs>
            <v-tabs-items v-model="tab">
              <v-tab-item>
                <v-card-text>
                  <Detail_cus
                    :myDetail="myDetail"
                    :ReadOn="$route.query.type == 'detail'"
                  ></Detail_cus>
                </v-card-text>
                <v-card-actions
                  class="justify-center"
                  v-if="$route.query.type != 'detail'"
                  
                >
                  <v-btn color="success" elevation="0" @click="fn_savecustomer" v-if="$route.query.type == 'add'">
                    <v-icon> mdi-content-save </v-icon>บันทึก
                  </v-btn>
                  <v-btn color="success" elevation="0" @click="fn_upadatecustomer" v-if="$route.query.type == 'edit'">
                    <v-icon> mdi-content-save </v-icon>บันทึก
                  </v-btn>
                </v-card-actions>
              </v-tab-item>
              <v-tab-item>
                <v-card-text>
                  <Treatment v-bind:myProfile="myProfile"></Treatment>
                </v-card-text>
              </v-tab-item>
            </v-tabs-items>
          </v-card>
        </v-col>
      </v-row>
    </div>
    <DailogNut ref="dailognut" @getdata="fn_getprofile($route.query.idcus)"></DailogNut>
  </div>
</template>

<script>
import axios from "axios";
import Detail_cus from "@/components/Customer/Detail_cus";
import Treatment from "@/components/Customer/Treatment";
import DailogNut from "@/components/Sub/DailogNut";
export default {
  name: "IndexPage",
  components: {
    Detail_cus,
    Treatment,
    DailogNut,
  },
  data() {
    return {
      tab: 0,
      myDetail: {},
      myProfile: {},
    };
  },
  methods: {
    async fn_getprofile(idcus) {
      await axios
        .get(`${process.env.api_url}/customer/profile?IDCus=${idcus}`, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.myProfile = res.data.data[0];
        })
        .catch((err) => {
          alert(err);
        });
    },
    async fn_getCustomer(idcus) {
      await axios
        .get(`${process.env.api_url}/customer?textSearch=&IDCus=${idcus}`, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.myDetail = res.data.data[0];
        })
        .catch((err) => {
          alert(err);
        });
    },
    async fn_savecustomer() {
      let data = JSON.stringify(this.myDetail);
      await axios
        .post(`${process.env.api_url}/customer/insert`, data, {
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
    async fn_upadatecustomer() {
      let data = JSON.stringify(this.myDetail);
      await axios
        .post(`${process.env.api_url}/customer/update`, data, {
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
        .post(`${process.env.api_url}/ConfigCon/delete_doctor`, data, {
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
    fn_addNut() {
      this.$refs.dailognut.open();
      this.$refs.dailognut.FormAdd.ID_customer = this.myDetail.ID_customer
      this.$refs.dailognut.FormAdd.name = `${this.myDetail.Fisrtname} ${this.myDetail.Lastname}`
      this.$refs.dailognut.FormAdd.tel = this.myDetail.tell
    },
  },
  async mounted() {
    if(this.$route.query.idcus){
     await this.fn_getCustomer(this.$route.query.idcus)
     await this.fn_getprofile(this.$route.query.idcus)
    }
  },
};
</script>