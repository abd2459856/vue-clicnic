<template>
  <v-app dark>
    <v-app-bar :clipped-left="clipped" fixed app color="white">
      <img src="~/static/logo2.jpg" alt="" height="45px" />
      <v-toolbar-title>{{ title }}</v-toolbar-title>
      <v-spacer />
      <div class="d-none d-sm-flex">
        <v-btn text to="/" color="deep-orange">
          <v-icon>mdi-calendar-month</v-icon>
          วันนี้
        </v-btn>
        <v-btn text to="/calendar/" color="deep-orange">
          <v-icon>mdi-calendar-month</v-icon>
          ปฎิทิน
        </v-btn>
        <v-btn to="/customer/" text color="deep-orange">
          <v-icon>mdi-account-tie</v-icon>
          คนไข้
        </v-btn>
        <v-btn to="/doctor/" text color="deep-orange">
          <v-icon>mdi-doctor</v-icon>
          แพทย์
        </v-btn>
      </div>
      <div class="d-flex d-sm-none">
        <v-menu>
          <template v-slot:activator="{ attrs, on }">
            <v-btn color="deep-orange" text v-bind="attrs" v-on="on"
              >เมนู
            </v-btn>
          </template>
          <v-list>
            <v-list-item to="/" color="deep-orange">
              <v-list-item-icon>
                <v-icon>mdi-calendar-month</v-icon>
              </v-list-item-icon>
              <v-list-item-title>วันนี้</v-list-item-title>
            </v-list-item>
            <v-list-item to="/calendar/" color="deep-orange">
              <v-list-item-icon>
                <v-icon>mdi-calendar-month</v-icon>
              </v-list-item-icon>
              <v-list-item-title>ปฎิทิน</v-list-item-title>
            </v-list-item>
            <v-list-item to="/customer/" color="deep-orange">
              <v-list-item-icon>
                <v-icon>mdi-account-tie</v-icon>
              </v-list-item-icon>
              <v-list-item-title>คนไข้</v-list-item-title>
            </v-list-item>
            <v-list-item to="/doctor/" color="deep-orange">
              <v-list-item-icon>
                <v-icon>mdi-doctor</v-icon>
              </v-list-item-icon>
              <v-list-item-title>แพทย์</v-list-item-title>
            </v-list-item>
          </v-list>
        </v-menu>
      </div>
      <v-btn icon @click.stop="rightDrawer = !rightDrawer">
        <v-icon>mdi-cog-outline</v-icon>
      </v-btn>
    </v-app-bar>
    <v-main style="background-color: rgb(234, 233, 226)">
      <v-container fluid>
        <Nuxt />
      </v-container>
    </v-main>
    <v-navigation-drawer v-model="rightDrawer" :right="right" temporary fixed>
      <v-list nav dense>
        <v-list-item to="/config/package_treat" router>
          <v-list-item-icon>
            <v-icon>mdi-cog</v-icon>
          </v-list-item-icon>
          <v-list-item-title>หัวข้อการรักษา</v-list-item-title>
        </v-list-item>
        <v-list-item to="/config/room_treat" router>
          <v-list-item-icon>
            <v-icon>mdi-cog</v-icon>
          </v-list-item-icon>
          <v-list-item-title>ห้องรักษา</v-list-item-title>
        </v-list-item>
        <v-list-item to="/report" router>
          <v-list-item-icon>
            <v-icon>mdi-file-chart</v-icon>
          </v-list-item-icon>
          <v-list-item-title>รายงาน</v-list-item-title>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-footer :absolute="!fixed" app>
      <span>&copy; {{ new Date().getFullYear() }}</span>
    </v-footer>
  </v-app>
</template>

<script>
export default {
  name: "DefaultLayout",
  data() {
    return {
      clipped: true,
      drawer: true,
      fixed: false,
      items: [
        {
          icon: "mdi-apps",
          title: "Welcome",
          to: "/",
        },
        {
          icon: "mdi-chart-bubble",
          title: "Inspire",
          to: "/inspire",
        },
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: "",
    };
  },
};
</script>
