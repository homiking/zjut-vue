<template>
<div class="header" style="height: 50px; line-height: 50px; border-bottom: 1px solid #ccc; display: flex">
  <div style="width: 200px; padding-left: 30px; font-weight: bold;color: cornflowerblue ">浙工大在线家园</div>
  <div style="flex: 1"></div>
  <div style="width: 100px">
    <el-dropdown>
    <span class="el-dropdown-link">
      <el-avatar
        :src=user.avatar
      />
        </span>
      <template #dropdown>
        <el-dropdown-menu>
          <el-dropdown-item @click="$router.push({ path: '/person' }) ">个人信息</el-dropdown-item>
          <el-dropdown-item @click="$router.push({ path: '/password' }) ">修改信息</el-dropdown-item>
          <el-dropdown-item @click="$router.push({ path: '/login' }) ">退出系统</el-dropdown-item>
        </el-dropdown-menu>
      </template>
    </el-dropdown>
  </div>
</div>
</template>

<script>  

export default {

  name: "Header",
  data(){
    return{
        user:JSON.parse(window.sessionStorage.getItem('user'))
        
      }
  },

}

</script>

<style scoped>
.el-dropdown-link {
  height: 50px;
  line-height: 50px;
  font-size: medium;
  cursor: pointer;
  color: var(--el-color-primary);
  display: flex;
  align-items: center;
  
}
.header{
   background-color:antiquewhite;
}

</style>