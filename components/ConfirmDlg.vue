<style scoped>
.icon-dailog {
  height: 60px;
  width: 60px;
  transform: rotateY(560deg);
  animation: turn 3.5s ease-out forwards 0.5s;
}

@keyframes turn {
  100% {
    transform: rotateY(0deg);
  }
}
.zoom-in-out-icon {
  margin: 10px;
  width: 50px;
  height: 50px;
  /* background: #f50057; */
  animation: zoom-in-zoom-out 1s ease infinite;
}

@keyframes zoom-in-zoom-out {
  0% {
    transform: scale(1, 1);
  }
  50% {
    transform: scale(1.2, 1.2);
  }
  100% {
    transform: scale(1, 1);
  }
}
</style>
<template>
  <div>
    <v-dialog
      v-model="dialog"
      :max-width="options.width"
      :style="{ zIndex: options.zIndex }"
      @keydown.esc="cancel"
      persistent
    >
      <v-card>
        <v-card-title class="justify-center">
          <img width="60px" height="60px"  class="zoom-in-out-icon" v-if="options.icon == 'info'" src="info.png" />
          <img width="60px" height="60px"  class="zoom-in-out-icon" v-if="options.icon == 'warning'" src="warning.png" />
          <img width="60px" height="60px"  class="zoom-in-out-icon" v-if="options.icon == 'error'" src="error.png" />
          <img width="60px" height="60px"  class="zoom-in-out-icon" v-if="options.icon == 'success'" src="success.png" />
        </v-card-title>
        <v-card-title class="text-h5 justify-center">{{ title }}</v-card-title>
        <v-card-text
          :style="`color:${options.messagecolor} !important;`"
          v-show="!!message"
          class="pt-0 pr-4 pl-4 black--text text-center"
          v-html="message"
        ></v-card-text>
        <v-card-actions class="pt-3">
          <v-spacer></v-spacer>
          <v-btn
            small
            v-if="options.noconfirm"
            :color="options.btnCancel"
            class="body-2 font-weight-bold"
            @click.native="cancel"
            >{{ options.btnCanceltext }}</v-btn
          >
          <v-btn
            v-if="options.confirm"
            small
            :color="options.btnOk"
            class="body-2 font-weight-bold"
            @click.native="agree"
            >{{ options.btnOktext }}</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog
      v-model="alert"
      :max-width="optionsalert.width"
      :style="{ zIndex: optionsalert.zIndex }"
      @keydown.esc="cancel"
    >
      <v-card>
        <v-card-title v-if="optionsalert.icon" class="justify-center pb-0">
          <img width="60px" height="60px"  class="zoom-in-out-icon" v-if="optionsalert.icon == 'info'" src="info.png" />
          <img width="60px" height="60px"  class="zoom-in-out-icon" v-if="optionsalert.icon == 'warning'" src="warning.png" />
          <img width="60px" height="60px"  class="zoom-in-out-icon" v-if="optionsalert.icon == 'error'" src="error.png" />
          <img width="60px" height="60px"  class="zoom-in-out-icon" v-if="optionsalert.icon == 'success'" src="success.png" />
        </v-card-title>
        <v-card-title v-if="title1" class="text-h5 justify-center"
          >{{ title1 }}
        </v-card-title>

        <v-card-text
          :style="`color:${optionsalert.messagecolor} !important;font-size: 16px`"
          v-show="!!messagealert"
          class="pa-4 black--text text-center"
          v-html="messagealert"
        ></v-card-text>
        <v-card-actions class="pt-3">
          <v-spacer></v-spacer>
          <v-btn
            small
            v-if="optionsalert.noconfirm"
            :color="optionsalert.btnCancel"
            class="body-2 font-weight-bold"
            @click.native="alert = false"
            >{{ optionsalert.btnCanceltext }}</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog
      v-model="dialogTextbox"
      :max-width="optionstxb.width"
      :style="{ zIndex: optionstxb.zIndex }"
      @keydown.esc="cancel"
      persistent
    >
      <v-card>
        <v-card-title class="justify-center">
          <img width="60px" height="60px"  class="zoom-in-out-icon" v-if="optionstxb.icon == 'info'" src="info.png" />
          <img width="60px" height="60px"  class="zoom-in-out-icon" v-if="optionstxb.icon == 'warning'" src="warning.png" />
          <img width="60px" height="60px"  class="zoom-in-out-icon" v-if="optionstxb.icon == 'error'" src="error.png" />
          <img width="60px" height="60px"  class="zoom-in-out-icon" v-if="optionstxb.icon == 'success'" src="success.png" />
        </v-card-title>
        <v-card-title class="text-h5 justify-center">{{
          titletxb
        }}</v-card-title>

        <v-card-text>
          <v-form ref="form">
            <v-row>
              <v-col cols="12">
                <v-text-field
                  v-model="remark"
                  :rules="[(v) => !!v || 'กรุณากรอก']"
                  required
                  outlined
                  dense
                  label="Remark"
                ></v-text-field>
              </v-col>
            </v-row>
          </v-form>
        </v-card-text>
        <v-card-text
          :style="`color:${optionstxb.messagecolor} !important;`"
          v-show="!!message"
          class="pa-4 black--text"
          v-html="message"
        ></v-card-text>
        <v-card-actions class="pt-3">
          <v-spacer></v-spacer>
          <v-btn
            small
            v-if="optionstxb.noconfirm"
            :color="optionstxb.btnCancel"
            class="body-2 font-weight-bold"
            @click.native="canceltxb"
            >{{ optionstxb.btnCanceltext }}</v-btn
          >
          <v-btn
            v-if="optionstxb.confirm"
            small
            :color="optionstxb.btnOk"
            class="body-2 font-weight-bold"
            @click.native="agreetxb"
            >{{ optionstxb.btnOktext }}</v-btn
          >
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogIMG" width="800">
      <v-card>
        <v-card-title>
          {{ ImageTitle }}
          <v-spacer></v-spacer>
          <v-btn icon small @click="dialogIMG = false">
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-card-title>
        <v-card-text>
          <v-img :src="pathImage"></v-img>
        </v-card-text>
      </v-card>
    </v-dialog>
    <v-dialog v-model="dialogLoading" persistent width="300">
      <v-card :color="loadingOption.color" dark>
        <v-card-text>
          {{loadingOption.title}}
            <v-progress-linear
              indeterminate
              color="white"
              class="mb-0"
            ></v-progress-linear>
        </v-card-text>
      </v-card>
    </v-dialog>
  </div>
</template>
    
    <script>
export default {
  name: "ConfirmDlg",
  data() {
    return {
      dialog: false,
      resolve: null,
      reject: null,
      message: null,
      title: null,
      options: {
        icon: false,
        color: "grey lighten-3",
        btnCancel: "secondary",
        btnCanceltext: "Cancel",
        btnOk: "success",
        btnOktext: "OK",
        width: 400,
        zIndex: 200,
        noconfirm: true,
        confirm: true,
        messagecolor: "black",
      },
      alert: false,
      resolve1: null,
      reject1: null,
      messagealert: null,
      title1: null,
      optionsalert: {
        icon: false,
        color: "grey lighten-3",
        btnCancel: "secondary",
        btnCanceltext: "Cancel",
        btnOk: "success",
        btnOktext: "OK",
        width: 400,
        zIndex: 200,
        noconfirm: true,
        confirm: true,
        messagecolor: "black",
      },

      dialogTextbox: false,
      resolvetxb: null,
      rejecttxb: null,
      messagetxb: null,
      titletxb: null,
      remark: null,
      optionstxb: {
        icon: false,
        color: "grey lighten-3",
        btnCancel: "secondary",
        btnCanceltext: "Cancel",
        btnOk: "success",
        btnOktext: "OK",
        width: 400,
        zIndex: 200,
        noconfirm: true,
        confirm: true,
        messagecolor: "black",
        valid: true,
      },
      dialogIMG: false,
      pathImage: "",
      ImageTitle: "",

      dialogLoading: false,
      loadingOption: {
        color: "primary",
        title: "Please stand by",
      },
    };
  },

  methods: {
    open(title, message, options) {
      this.dialog = true;
      this.title = title;
      this.message = message;
      this.options = Object.assign(this.options, options);
      return new Promise((resolve, reject) => {
        this.resolve = resolve;
        this.reject = reject;
      });
    },
    agree() {
      this.resolve(true);
      this.dialog = false;
    },
    cancel() {
      this.resolve(false);
      this.dialog = false;
    },
    dailogalert(title, message, options) {
      this.alert = true;
      this.title1 = title;
      this.messagealert = message;
      this.optionsalert = Object.assign(this.optionsalert, options);
      // return new Promise((resolve, reject) => {
      //   this.resolve1 = resolve;
      //   this.reject1 = reject;
      // });
    },
    agree1() {
      this.resolve(true);
      this.alert = false;
    },
    cancel1() {
      this.resolve(false);
      this.alert = false;
    },
    dialogtextbox(title, message, options) {
      console.log(options);
      this.remark = "";
      this.titletxb = title;
      this.messagetxb = message;
      this.optionstxb = Object.assign(this.optionstxb, options);
      this.dialogTextbox = true;
      return new Promise((resolvetxb, rejecttxb) => {
        this.resolvetxb = resolvetxb;
        this.rejecttxb = rejecttxb;
      });
    },
    agreetxb() {
      if (this.optionstxb.valid) {
        if (!this.$refs.form.validate()) return;
      }
      this.resolvetxb({ conf: true, remark: this.remark });
      this.dialogTextbox = false;
    },
    canceltxb() {
      this.resolvetxb({ conf: false, remark: this.remark });
      this.dialogTextbox = false;
    },
    view_IMG(filename, title) {
      this.dialogIMG = true;
      this.pathImage = filename;
      this.ImageTitle = title;
      // this.messagetxb = message;
      // this.optionstxb = Object.assign(this.optionstxb, options);
      // return new Promise((resolvetxb, rejecttxb) => {
      //   this.resolvetxb = resolvetxb;
      //   this.rejecttxb = rejecttxb;
      // });
    },
    openloadging(options) {
      this.dialogLoading = true;
      this.loadingOption = Object.assign(this.loadingOption, options);
    },
    closeloadging() {
      this.dialogLoading = false;
    },
  },
};
</script>
    