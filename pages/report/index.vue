<style scoped>
::v-deep .costomgray > .v-input__control > .v-input__slot {
  background-color: white;
}
</style>
<template>
  <div>
    <div class="text-center mt-5">
      <v-row justify="center">
        <v-col md="2">
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
                v-model="dateStart"
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
              v-model="dateStart"
              color="deep-orange"
              @input="menuStart = false"
            ></v-date-picker>
          </v-menu>
        </v-col>
        <v-col md="2">
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
                v-model="dateEnd"
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
              v-model="dateEnd"
              color="deep-orange"
              @input="menuEnd = false"
            ></v-date-picker>
          </v-menu>
        </v-col>
        <v-col md="1">
          <v-btn elevation="0" color="primary" @click="fn_getData()">
            <v-icon>mdi-magnify</v-icon>
            ค้นหา
          </v-btn>
        </v-col>
      </v-row>
    </div>
    <v-row class="mt-3">
      <v-col md="4" sm="6" cols="12">
        <v-card elevation="0">
          <v-card-title
            class="green text-h5 justify-center font-weight-bold"
            style="color: white"
          >
            รายได้ทั้งหมด
          </v-card-title>
          <br />
          <div class="pl-3 pr-3" style="height: 30vh;overflow: auto;">
            <div class="text-center">
              <h1 style="color: green">{{ numberFormat(sumCost, 2) }} บาท</h1>
            <p>
              จำนวน
              {{
                numberFormat(
                  sumall.reduce((a, b) => a + parseFloat(b.amount), 0)
                )
              }}
              รายการ
            </p>
          </div>
            <table width="100%">
              <tr class="" v-for="(item, i) in sumall" :key="i">
                <td width="30%" class="text-left">{{ item.treat_name }}</td>
                <td width="30%" class="text-right">
                  {{ numberFormat(item.cost, 2) }} ฿
                </td>
                <td width="30%" class="text-right">
                  {{ numberFormat((parseFloat(item.cost) / sumCost) * 100, 2) }}
                  %
                </td>
              </tr>
            </table>
          </div>
          <br />
        </v-card>
      </v-col>
      <v-col md="4" sm="6" cols="12">
        <v-card elevation="0">
          <v-card-title
            class="cyan text-h5 justify-center font-weight-bold"
            style="color: white"
          >
            รายการเงินได้สูงสุด
          </v-card-title>
          <br />
          <div class="pl-3 pr-3" style="height: 30vh;overflow: auto;">
            <table width="100%">
              <tr class="" v-for="(item, i) in maxsum" :key="i">
                <td width="30%" class="text-left">{{ item.treat_name }}</td>
                <td width="30%" class="text-left">
                  {{ numberFormat(item.amount) }} รายการ
                </td>
                <td width="30%" class="text-right">
                  {{ numberFormat(item.cost, 2) }} ฿
                </td>
              </tr>
            </table>
          </div>
          <br />
        </v-card>
      </v-col>
      <v-col md="4" sm="6" cols="12">
        <v-card elevation="0">
          <v-card-title
            class="indigo text-h5 justify-center font-weight-bold"
            style="color: white"
          >
            รายการยอดนิยม
          </v-card-title>
          <br />
          <div class="pl-3 pr-3" style="height: 30vh;overflow: auto;">
            <table width="100%">
              <tr class="" v-for="(item, i) in sumhit" :key="i">
                <td width="30%" class="text-left">{{ item.treat_name }}</td>
                <td width="30%" class="text-left">
                  {{ numberFormat(item.amount) }} รายการ
                </td>
                <td width="30%" class="text-right">
                  {{ numberFormat(item.cost, 2) }} ฿
                </td>
              </tr>
            </table>
          </div>
          <br />
        </v-card>
      </v-col>
      <v-col md="4" sm="6" cols="12">
        <v-card elevation="0">
          <v-card-title
            class="teal text-h5 justify-center font-weight-bold"
            style="color: white"
          >
            ผู้ใช้จ่ายสูงสุด
          </v-card-title>
          <br />
          <div class="pl-3 pr-3" style="height: 30vh;overflow: auto;">
            <table width="100%">
              <tr class="" v-for="(item, i) in maxcus" :key="i">
                <td width="30%" class="text-left">
                  {{ item.Fisrtname }} {{ item.Lastname }}
                </td>
                <td width="30%" class="text-left">
                  {{ numberFormat(item.amount) }} รายการ
                  <v-btn
                    elevation="0"
                    color="teal lighten-1"
                    x-small
                    icon
                    @click="loadExcel(item.ID_customer)"
                  >
                    <v-icon>mdi-file-excel-outline</v-icon>
                  </v-btn>
                </td>
                <td width="30%" class="text-right">
                  {{ numberFormat(item.cost, 2) }} ฿
                </td>
              </tr>
            </table>
          </div>
          <br />
        </v-card>
      </v-col>
    </v-row>
    <ConfirmDlg ref="confirm" />
  </div>
</template>
  
<script>
import axios from "axios";
import ConfirmDlg from "@/components/ConfirmDlg.vue";
export default {
  components: {
    ConfirmDlg,
  },
  name: "InspirePage",
  data: () => ({
    sumall: [],
    maxsum: [],
    sumhit: [],
    maxcus: [],
    sumCost: 0,
    textSearch: "",
    menuStart: false,
    menuEnd: false,
    dateStart: new Date(Date.now() - new Date().getTimezoneOffset() * 60000)
          .toISOString()
          .substr(0, 10),
    dateEnd: new Date(Date.now() - new Date().getTimezoneOffset() * 60000)
          .toISOString()
          .substr(0, 10),
  }),
  methods: {
    async fn_getData() {
      await axios
        .get(
          `${process.env.api_url}/report?dateStart=${this.dateStart}&dateEnd=${this.dateEnd}`,
          {
            headers: {
              "Content-Type": "application/json",
            },
          }
        )
        .then((res) => {
          this.sumall = res.data.data.sumall;
          this.maxsum = res.data.data.maxsum;
          this.sumhit = res.data.data.sumhit;
          this.maxcus = res.data.data.maxcus;
          this.sumCost = this.sumall.reduce(
            (a, b) => a + parseFloat(b.cost),
            0
          );
        })
        .catch((err) => {
          alert(err);
        });
    },
    async loadExcel(ID_customer) {
      window.open(
        `${process.env.api_url}/Customer_con/Export_Excel?ID_customer=${ID_customer}`,
        "_blank"
      );
    },
  },
  mounted() {
    this.fn_getData();
  },
};
</script>
  