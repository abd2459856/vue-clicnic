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
          <v-btn icon dark @click="dialog = false">
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-toolbar>
        <br />
        <v-row>
          <v-col md="5">
            <div style="height: 85vh; overflow: auto">
              <v-list two-line>
                <v-list-item-group v-model="selectHe" active-class="pink--text">
                  <template v-for="(item, index) in Treatment">
                    <v-list-item
                      :key="item.title"
                      @change="fn_detaailTreat(item)"
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
          <v-col md="7">
            <div
              v-if="selectHe != null && selectHe >= 0"
              style="height: 85vh; overflow: auto"
            >
              <v-row>
                <v-col v-for="n in 9" :key="n" cols="12">
                  <v-img
                    :src="`https://picsum.photos/500/300?image=${n * 5 + 10}`"
                    :lazy-src="`https://picsum.photos/10/6?image=${n * 5 + 10}`"
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
  </div>
</template>
<script>
import axios from "axios";
export default {
  props: {
    myProfile: Object,
  },
  data() {
    return {
      groupTreatment: [],
      Treatment: [],
      dialog: false,
      detaailHe: {},
      selectHe: null,
    };
  },
  methods: {
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
      await axios
        .get(`${process.env.api_url}/treatment?IDCus=${this.$route.query.idcus}&ID_treat=${item.ID_treat}`, {
          headers: {
            "Content-Type": "application/json",
          },
        })
        .then((res) => {
          this.Treatment = res.data.data;
        })
        .catch((err) => {
          alert(err);
        });

      this.dialog = true;
    },
    // fn_detaailTreat(item) {
    //   this.detaailHe = item;
    // },
  },
  async mounted() {
    if (this.$route.query.idcus) {
      await this.fn_getData(this.$route.query.idcus);
    }
  },
};
</script>
