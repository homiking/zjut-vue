<template>
  <div>
      <!--    头部-->
  <Header :user="user"></Header>
<!--    主体-->
    <div class="main" style="display:flex ">
<!--      侧边栏-->
      <Aside></Aside>
      <router-view style="flex:1px"></router-view>
      <!--      内容区域-->
    </div>
  </div>
</template>

<script>
import Header from "@/components/Header";
import Aside from "@/components/Aside";
import request from "@/utils/request";

export default {
    name:"Layout",
    components: {
    Header,
    Aside
  },
  data(){
    return {
      user:{}
    }
  },
  created(){
    this.refreshUser()
  },
  methods: {
    refreshUser() {
      let userJson = sessionStorage.getItem("user");
      if (!userJson) {
        return
      }
      let userId = JSON.parse(userJson).id
      // 从后台取出更新后的最新用户信息
      request.get("/user/" + userId).then(res => {
        this.user = res.data
      })
    }
  }
}
</script>

<style>
.main{
   background-color:antiquewhite;
}

</style>