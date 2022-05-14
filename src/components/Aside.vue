<template>
  <div class="aside">
    <el-menu
        style="width: 200px; min-height: calc(100vh - 50px)"
        :default-active="$route.path"
        router
    >
      <div  v-for="m in user.permissions" :key="m.id">
        <el-menu-item :index="m.path" v-if="m.name !== 'Person' && m.name !== 'Password' ">
         {{ m.comment }}
        </el-menu-item>
      </div>
    </el-menu>
  </div>
</template>

<script>
import Header from "./Header.vue"
export default{
    name: "Aside",
    data() {
        return {
            user: {}
        };
    },
    created() {
        let userStr = sessionStorage.getItem("user") || "{}";
        this.user = JSON.parse(userStr);
        // 请求服务端，确认当前登录用户的 合法信息
        // request.get("/user/" + this.user.id).then(res => {
        //   if (res.code === '0') {
        //     this.user = res.data
        //   }
        // })
    },
    components: { Header }
}

</script>

<style scoped>
.el-menu-vertical-demo:not(.el-menu--collapse) {
  width: 200px;
  min-height: 400px;
}
.aside{
  background-color: black;
}
.el-menu{
  background-color:antiquewhite;
}
.el-sub-menu{
  background-color:antiquewhite;
}
.el-menu-item-group{
  background-color:antiquewhite;
}

</style>