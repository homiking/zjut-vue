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
        placeholder="输入新闻名称搜索"
      />
      <el-button type="primary" style="margin-left: 6px"  @click="load">查询</el-button>
    </div>
    <!--  表格主体-->
    <el-table :data="tableData" stripe style="width: 100%">
      <div v-if="isShow">
        <el-table-column prop="id" label="ID"  sortable/>
      </div>
      
      <el-table-column prop="title" label="标题"  />
      
      <el-table-column  label="详情"  >
        <template #default="scope">
            <el-button size="small" @click="details(scope.row)">详情</el-button>
        </template>
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
      <el-dialog title="提示" v-model="dialogVisible" width="50%">

        <el-form-item label="标题">
              <el-input v-model="form.title" style="width: 50%"></el-input>
            </el-form-item> 
        <div id="div1"></div>
            <!-- <el-form :model="form" label-width="120px">
              <el-form-item label="标题">
              <el-input v-model="form.title" style="width: 50%"></el-input>
            </el-form-item> 

            
           <el-form-item label="内容">
              <el-input v-model="form.content" style="width: 80%"></el-input>
            </el-form-item>    
          
         </el-form> -->
        <template #footer>
          <span class="dialog-footer">
            <el-button @click="dialogVisible = false">取消</el-button>
            <el-button type="primary" @click="save">确定</el-button>
          </span>
        </template>
      </el-dialog>
      <el-dialog title="详情" v-model="vis" width="50%">
          <el-card>
            <div v-html="detail.content" style="min-height: 100px"></div>
          </el-card>
      </el-dialog>


    </div>
  </div>
</template>

<script>
// @ is an alias to /src
import E from 'wangeditor'
import request from "@/utils/request";

let editor;
export default {
  name: "News",
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
    // 封面上传钩子
    details(row) {
      this.detail = row
      this.vis = true
    },
    filesUploadSuccess(res) {
      console.log(res)
      this.form.cover = res.data
    },
    // 删除
    handleClick(id){
      request.delete("/news/deleteNews/" + id).then(res =>{
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
      request.get("/news/newsQueryPage",{
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
      this.$nextTick(() => {
        // 关联弹窗里面的div，new一个 editor对象
        if (!editor) {
          editor = new E('#div1')

          // 配置 server 接口地址
          editor.config.uploadImgServer = 'http://118.31.251.4:9200/file/editor/upload/oss'
          editor.config.uploadFileName = "file"  // 设置上传参数名称
          
          editor.create()
        }

        editor.txt.html("")

      })
    },
    // 保存新增, 以及修改
    save(){
      this.form.content = editor.txt.html()  // 获取 编辑器里面的值，然后赋予到实体当中
       if(this.form.id) {
         request.put("/news/editNews",this.form).then(res =>{
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
         request.post("/news/add",this.form).then(res =>{
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

      this.$nextTick(() => {
        // 关联弹窗里面的div，new一个 editor对象
        // 关联弹窗里面的div，new一个 editor对象
        if (!editor) {
          editor = new E('#div1')

          // 配置 server 接口地址
          editor.config.uploadImgServer = 'http://118.31.251.4:9200/file/editor/upload/oss'
          editor.config.uploadFileName = "file"  // 设置上传参数名称
          editor.config.uploadImgMaxSize= 3 * 1024 * 1024
          editor.config.uploadImgHooks = {
            before : function(xhr, editor, files) {
			
		},
		success : function(xhr, editor, result) {
			console.log("上传成功");
		},
		fail : function(xhr, editor, result) {
			console.log("上传失败,原因是"+result);
		},
		error : function(xhr, editor) {
			console.log("上传出错");
		},
		timeout : function(xhr, editor) {
			console.log("上传超时");
		}
          }
          editor.create()
        }

        editor.txt.html(row.content)
      })
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
