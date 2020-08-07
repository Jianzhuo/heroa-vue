<template>
  <div class="mod-home">
    <h3>HI 办公平台</h3>

    <div v-if="this.$store.state.user.role.includes('constructor')">
      <div class="home-report-thumb" v-on:click="clickReportForm">
        <img src="~@/assets/img/daily-report-thumb.png" />
      </div>
    </div>

    <div v-if="this.$store.state.user.role.includes('admin')">
      <ul>
        <li>平台管理--用户管理 权限管理 菜单管理</li>
        <li>日报系统--工人日报</li>
      </ul>
      <div class="home-admin-ui">
        <div class="home-admin-block">
          <div class="home-admin-block-img">
            <img src="~@/assets/img/home-report-1.jpg" />
          </div>
          <div class="home-admin-block-text">
            <h2>目前用户</h2>
            <h1>
              100
              <span>人</span>
            </h1>
          </div>
          <div class="home-admin-btn" v-on:click="clickUserList">查看页面 <i class="el-icon-arrow-right"></i></div>
        </div>
        <div class="home-admin-block">
          <div class="home-admin-block-img">
            <img src="~@/assets/img/home-report-2.jpg" />
          </div>
          <div class="home-admin-block-text">
            <h2>今日日报</h2>
            <h1>
              100
              <span>份</span>
            </h1>
          </div>
          <div class="home-admin-btn" v-on:click="clickReportList">查看页面 <i class="el-icon-arrow-right"></i></div>
        </div>
        <div class="home-admin-block">
          <div class="home-admin-block-img">
            <img src="~@/assets/img/home-report-3.jpg" />
          </div>
          <div class="home-admin-block-text">
            <h2>当前项目</h2>
            <h1>
              100
              <span>项</span>
            </h1>
          </div>
          <div class="home-admin-btn" v-on:click="clickLocationList">查看页面 <i class="el-icon-arrow-right"></i></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Home",
  data: () => ({}),
  methods: {
    clickReportForm() {
      this.$router.push('dreport-ReportForm')
    },
    clickUserList() {
      this.$router.push('sys-user')
    },
    clickReportList() {
      this.$router.push('dreport-report')
    },
    clickLocationList() {
      this.$router.push('dreport-worklocation')
    },
  },
};
</script>

<style>
.mod-home {
  line-height: 1.5;
}
.home-report-thumb {
  width: 50%;
  display: inline-block;
}
.home-report-thumb img {
  width: 100%;
}
.home-admin-ui {
}
.home-admin-block {
  width: 25%;
  margin: 0 0.5%;
  display: inline-block;
  border-radius: 25px;
  overflow: hidden;
  position: relative;
}
.home-admin-block-img {
}
.home-admin-block-img img {
  width: 100%;
}
.home-admin-block-text {
  position: absolute;
  z-index: 1;
  padding: 20px;
  top: 0;
}
.clear {
  clear: both;
}
.home-admin-block-text h1 {
  font-size: 55px;
  margin: 0;
}
.home-admin-block-text h1 span {
  font-size: 15px;
}
.home-admin-block-text h2 {
}
.home-admin-btn {
  position: absolute;
  bottom: 0;
  margin: 30px 20px;
  font-size: 15px;
  background-color: #871719;
  padding: 8px 15px;
  color: #fff;
  border-radius: 8px;
}
@media screen and (max-width: 600px) {
  .home-report-thumb {
    width: 100%;
  }
  .home-admin-block{
    width:100%;
    margin:1.5% 1%;
  }
}
</style>

