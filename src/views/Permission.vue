<template>
  <div style="padding: 10px">
    <!--  功能区域-->
    <div style="margin: 10px">
      <el-button type="primary" @click="add">新增</el-button>
      <el-button type="primary">导入</el-button>
      <el-button type="primary">导出</el-button>
    </div>
    <!--    搜索区域-->
    <div style="margin: 10px">
      <el-input
        v-model="search"
        style="width: 20%"
        placeholder="输入资源名称搜索"
      />
      <el-button type="primary" style="margin-left: 6px"  @click="load">查询</el-button>
    </div>
    <!--  表格主体-->
    <el-table :data="tableData" stripe style="width: 100%">
      <div v-if="isShow">
        <el-table-column prop="id" label="ID"  sortable/>
      </div>
      <el-table-column
          prop="name"
          label="名称">
      </el-table-column>
      <el-table-column
          prop="path"
          label="路径">
      </el-table-column>
      <el-table-column
          prop="comment"
          label="备注">
      </el-table-column>
      <el-table-column
          prop="icon"
          label="图标">
      </el-table-column>
      <!-- <el-table-column prop="author" label="作者" />
      <el-table-column prop="time" label="日期" /> -->
      <el-table-column   label="操作">

        <template #default="scope">
          <el-button size="small" @click="handleEdit(scope.row)">编辑</el-button>
          <el-popconfirm title="确定删除吗？" @confirm="handleClick(scope.row.id)">
            <template #reference>
              <el-button size="small" type="danger">删除</el-button>
            </template>
          </el-popconfirm>
        </template>
      </el-table-column>
    </el-table>
    <!--    分页功能，加到表格下面-->
    <div style="margin: 10px 0">
      <el-pagination
        v-model:currentPage="currentPage"
        v-model:page-size="pageSize"
        :page-sizes="[2, 5, 10]"
        layout="total, sizes, prev, pager, next, jumper"
        :total="total"
        @size-change="handleSizeChange"
        @current-change="handleCurrentChange"
      />
      <!-- 弹出框，新增的 -->
      <el-dialog title="提示" v-model="dialogVisible" width="30%">
      <el-form :model="form" label-width="120px">
        <el-form-item label="名称">
          <el-input v-model="form.name" style="width: 80%"></el-input>
        </el-form-item>
        <el-form-item label="路径">
          <el-input v-model="form.path" style="width: 80%"></el-input>
        </el-form-item>
        <el-form-item label="备注">
          <el-input v-model="form.comment" style="width: 80%"></el-input>
        </el-form-item>
        <el-form-item label="图标">
          <el-input v-model="form.icon" style="width: 80%"></el-input>
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
  </div>
</template>

<script>
import request from "@/utils/request";
export default {
  name: "Permission",
  components: {},
  data() {
    return {
      value:'',
      form:{},
      isShow:false,
      dialogVisible: false,
      search: '',
      currentPage: 1,
      pageSize: 5,
      total: 0,
      tableData: [
        
      ],
      detail: {},
      vis: false,
    }
  },
  // 页面自动加载，运行 load 方法
  created(){
    this.load();
  },
  methods: {
    // 编辑
    handleEdit(row){
      this.form = JSON.parse(JSON.stringify(row));
      this.dialogVisible = true;
    },
    // 删除
    handleClick(id){
      request.delete("/permission/deletePermission/" + id).then(res =>{
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
      request.get("/permission/permissionQueryPage",{
        params:{
        search:this.search,
        pageNum: this.currentPage,
        pageSize: this.pageSize
        }
      }).then(res =>{
         
       // console.log(res.data)
       // console.log(res.data.records[0].producttime)
        
        this.tableData = res.data.records;
        this.total = res.data.total;
      });
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
         request.put("/permission/editPermission",this.form).then(res =>{
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
         request.post("/permission/add",this.form).then(res =>{
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
    //
    handleEdit(row) {
      this.form = JSON.parse(JSON.stringify(row))
      this.dialogVisible = true
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
};

</script>
