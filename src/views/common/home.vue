<template>
  <div class="mod-home">
    <h3>HI 办公平台</h3>
    <ul>
      <li>平台管理--用户管理 权限管理 菜单管理</li>
      <li>日报系统--工人日报</li>
    </ul>

    <div v-if="this.$store.state.user.role.includes('constructor')">
      建筑工人展示区
    </div>

     {{this.$store.state.user.role}}
  </div>
</template>

<script>
export default {
  data() {
    return {
    };
  }
};
</script>

<style>
.mod-home {
  line-height: 1.5;
}
</style>

