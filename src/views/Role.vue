<template>
  <div style="padding: 10px">
    <!--    功能区域-->
    <div style="margin: 10px 0">
      <el-button type="primary" @click="add">新增</el-button>
    </div>

    <!--    搜索区域-->
    <div style="margin: 10px 0">
      <el-input v-model="search" placeholder="请输入关键字" style="width: 20%" clearable></el-input>
      <el-button type="primary" style="margin-left: 5px" @click="load">查询</el-button>
    </div>
    <el-table
        v-loading="loading"
        :data="tableData"
        border
        stripe
        style="width: 100%">
      <el-table-column
          prop="id"
          label="ID"
          sortable
          width="80"
      >
      </el-table-column>
      <el-table-column
          prop="name"
          label="名称">
      </el-table-column>
      <el-table-column
          prop="comment"
          label="备注">
      </el-table-column>
      <el-table-column label="权限菜单">
        <template #default="scope">
          <el-select clearable v-model="scope.row.permissions" multiple placeholder="请选择" style="width: 80%">
            <el-option v-for="item in permissions" :key="item.id" :label="item.comment" :value="item.id"></el-option>
          </el-select>
        </template>
      </el-table-column>
      <el-table-column label="操作">
        <template #default="scope">
          <el-button size="small" type="primary" @click="handleChange(scope.row)">保存权限菜单</el-button>
          <el-button size="small" @click="handleEdit(scope.row)">编辑</el-button>
          <el-popconfirm title="确定删除吗？" @confirm="handleDelete(scope.row.id)">
            <template #reference>
              <el-button size="small" type="danger">删除</el-button>
            </template>
          </el-popconfirm>
        </template>
      </el-table-column>
    </el-table>

    <div style="margin: 10px 0">
      <el-pagination
          @size-change="handleSizeChange"
          @current-change="handleCurrentChange"
          :current-page="currentPage"
          :page-sizes="[5, 10, 20]"
          :page-size="pageSize"
          layout="total, sizes, prev, pager, next, jumper"
          :total="total">
      </el-pagination>
    </div>

    <el-dialog title="提示" v-model="dialogVisible" width="30%">
      <el-form :model="form" label-width="120px">
        <el-form-item label="名称">
          <el-input v-model="form.name" style="width: 80%"></el-input>
        </el-form-item>
        <el-form-item label="备注">
          <el-input v-model="form.comment" style="width: 80%"></el-input>
        </el-form-item>
      </el-form>
      <template #footer>
          <span class="dialog-footer">
            <el-button @click="dialogVisible = false">取 消</el-button>
            <el-button type="primary" @click="save">确 定</el-button>
          </span>
      </template>
    </el-dialog>

  </div>
</template>

<script>

import request from "@/utils/request";

export default {
  name: 'Role',
  components: {},
  data() {
    return {
      loading: true,
      form: {},
      dialogVisible: false,
      search: '',
      currentPage: 1,
      pageSize: 10,
      total: 0,
      tableData: [],
      permissions: []
    }
  },
  created() {
    this.load()
  },
  methods: {
    handleChange(row) {
      request.put("/role/changePermission", row).then(res => {
        if (res.code === '0') {
          this.$message.success("更新成功")
          if (res.data) {
            this.$router.push("/login")
          }
        }
      })
    },
    // 编辑
    handleEdit(row){
      this.form = JSON.parse(JSON.stringify(row));
      this.dialogVisible = true;
    },
    // 删除
    handleClick(id){
      request.delete("/role/deleteRole/" + id).then(res =>{
        if(res.code === '0') {
           this.$message({
           type:"success",
           message:"删除成功"
            });
           } else {
           this.$message({
           type:"error",
           message:res.msg
              })
             }
          this.load() 
         })
      
    },
    // 加载
    load(){
      this.loading = true
      request.get("/role/roleQueryPage",{
        params:{
        search:this.search,
        pageNum: this.currentPage,
        pageSize: this.pageSize
        }
      }).then(res =>{
         
       // console.log(res.data)
       // console.log(res.data.records[0].producttime)
        this.loading = false
        this.tableData = res.data.records;
        this.total = res.data.total;
      });
      request.get("/permission/all").then(res => {
        this.permissions = res.data
      })
      console.log("调用了load 方法");
    },
    // 新增：转向增加页面
    add() {
      this.dialogVisible = true;
      this.form={};
    },
    // 保存新增, 以及修改
    save(){
       if(this.form.id) {
         request.put("/role/editRole",this.form).then(res =>{
           if(res.code === '0') {
           this.$message({
           type:"success",
           message:"修改成功"
            });
           } else {
           this.$message({
           type:"error",
           message:res.msg
              })
             }
          this.load()
          this.dialogVisible = false   
         })
      } else {
         request.post("/role/add",this.form).then(res =>{
           console.log("进入了新增")
          if(res.code === '0'){
            this.$message({
           type:"success",
           message:"新增成功"
            });
          } else {
            this.$message({
           type:"error",
           message:res.msg
              })
          }
          this.load()
          this.dialogVisible = false   
       });
       }
         
    },
    // 页面大小更改方法
    handleSizeChange(val) {
      val = this.pageSize;
      this.load();
      console.log("更改页面大小");
    },
    // 页码更改方法
    handleCurrentChange(val) {
      val = this.pageNum;
      this.load();
      console.log("更改页码");
    },
  }
}
</script>
