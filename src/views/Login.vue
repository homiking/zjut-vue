<template>
  <div
    style="width: 100%; height: 100vh; background-color:cadetblue; overflow: hidden"
  >
    <div style="width: 400px; margin: 150px auto" >
      <div style="color:aqua; font-size: 30px; text-align:center; padding: 30px 0" >
        欢迎登录
      </div>
      <el-form
        :model="form"
        size="large"
        v-model="loginDialogVisible"
      >
        <el-form-item  >
          <el-input prefix-icon="avatar"    placeholder="请输入登录名"  v-model="form.username">
            <!-- <template #prefix>
            <el-icon class="el-input__icon"><user /></el-icon>
          </template> -->
          </el-input>
        </el-form-item>
        <el-form-item >
          <el-input placeholder="请输入密码" prefix-icon="star"  v-model="form.password" show-password/>
        </el-form-item>
        <el-form-item >
          <el-button style="width:100%; " type="primary" @click="login">登录</el-button>
        </el-form-item>
        <el-form-item>
              <el-button type="text" style="color:brown" @click="$router.push('/register')">
                前往注册  
              </el-button>
            </el-form-item>
      </el-form>
    </div>
  </div>
</template>

<script>
import { activeRouter } from "@/utils/permission";
import request from "@/utils/request";

//import Cookies from 'js-cookie'
export default {
  name: "Login",
  components:{
  },
  data(){
    return{
        //注册表单相关
        registerDialogVisible:false,
        loginDialogVisible:true,
        formLabelWidth: '150px',
        form:{
            username:'',
            password:'',
            
        }
    }
  },
  methods:{
      // 登录
      login(){
          request.post("/user/login",this.form).then(res=>{
            if(res.code === '0') {
           this.$message({
           type:"success",
           message:"登录成功",
            })
           sessionStorage.setItem("user",JSON.stringify(res.data))//缓存用户信息
           const permissions = res.data.permissions;
           //初始化路由信息
           activeRouter(permissions)
           console.log("初始化路由信息");
           //this.$router.push({ path: 'supplier' }) 
           this.$router.push("/")  //登录成功之后进行页面的跳转，跳转到主页
           console.log("supplier重定向");
           } else {
           this.$message({
           type:"error",
           message:res.msg
              })
             }
          })
      },
     showRegistryDialog(){
         this.registerDialogVisible =  true
         this.loginDialogVisible = false

     },
     closeRegisterDialog(done){
        this.registerForm={//清空表单
          username:'',
          password:'',
          confirm:'',
        };
        //this.$refs.upload.clearFiles();//清除上传组件的图片
        done();//关闭对话框
      },
     
  }
};
</script>

<style>
</style>