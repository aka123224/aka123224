<!--html网页结构  /身材
css 美化页面 css /化妆
js  交互 动画 /谈吐
-->

<!--模板  -->
<template>
    <div>
        <!--按钮 -->
         <el-button type="success" size="small" @click="toAddHander"> 添加</el-button>
         <el-button type="danger"  size="small">批量删除</el-button>
<!--表格 -->
         <el-table :data="customers">
                <el-table-column prop="id" label="编号"></el-table-column>
             <el-table-column prop="realname" label="姓名"></el-table-column>
             <el-table-column prop="telephone" label="联系方式"></el-table-column>
             <el-table-column label="操作">
                 <template v-slot="slot">
                    <a  class="el-icon-delete" href="" @click.prevent="toDeleteHander(slot.row.id)"></a>
                    <a  class="el-icon-edit-outline" href="" @click.prevent="toUpdateHander(slot.row)"></a>
                 </template>
             </el-table-column>
         </el-table>
         <!--表格结束-->
         <!--分页-->
         <el-pagination
    layout="prev, pager, next"
    :total="50">
  </el-pagination>
  <!--分页结束-->
   <!--模态框-->
  <el-dialog
  :title="title"
  :visible.sync="visible"
  width="60%">
{{form}}
<el-form :model="form" label-width="80px">
  <el-form-item label="用户名">
    <el-input v-model="form.username"></el-input>
  </el-form-item>
   <el-form-item label="密码">
    <el-input type="password" v-model="form.password"></el-input>
  </el-form-item>
   <el-form-item label="真实姓名">
    <el-input  v-model="form.realname"></el-input>
  </el-form-item>
   <el-form-item label="手机号">
    <el-input  v-model="form.telephone"></el-input>
  </el-form-item>
</el-form>
  <span slot="footer" class="dialog-footer">
    <el-button size="small" @click="closeModalHander">取 消</el-button>
    <el-button size="small" type="primary" @click="submitHander">确 定</el-button>
  </span>
</el-dialog>
 <!--模态框jiesu-->
    </div>
</template>

<!--脚本-->
<script>
import request from '@/utils/request'
import querystring from 'querystring' //querystring 库是一个系统自带库，可以将对象转换查询为字符串
export default {
    //用于存放网页中需要调用的方法
    methods:{
      loadData(){
        let url ="http://localhost:6677/customer/findAll"
      request.get(url).then((response)=>{
        //将查询结果设置到customer中,this指向外部函数的this
         this.customers = response.data;
      })
      },

      submitHander(){
//调用request与后台进行交互，并且携带参数
//this.form对象---字符串-->后台
let url = "http://localhost:6677/customer/saveOrUpdate"
request({
  url,
  method:"POSt",
  headers:{
    "Content-Type":"application/x-www-form-urlencoded"
  },
  data:querystring.stringify(this.form)//querystring库  、form双向绑定，在添加用户的时候输入的信息就会保存在form中    冒号代表数据引用，@代表方法的指定
}).then((response)=>{
  //模态框关闭
  this.closeModalHandler();
  //刷新
  this.loadData()
  //提示消息
  this.$message({
    type:"success",
    message:response.message
  })
})

      },
      
        toDeleteHander(id){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //调用后台接口
          let url = "http://localhost:6677/customer/deleteById?id="+id;
      request.get(url).then((response)=>{
        //刷新数据
        this.loadData();
        //提示结果
        this.$message({
            type: 'success',
            message:response.message
          });
      })
          
        })
          },
      
        toUpdateHander(row){
        //模态框的表单中显示当前行的信息
        this.form = row;
        this.title = "修改顾客信息";
            this.visible = true;
        },
        closeModalHandler(){
       this.visible = false;
        },

 
        toAddHander(){
          this.form ={
            type:"customer"
          }
          this.title = "录入顾客信息";
            this.visible = true;

        }

    },
    //要向网页存放的数据
    data(){
        return{ 
          title:"录入员工信息",
          visible:false,
          customers:[ ],
          form:{
            type:"customer"
            
          }
        }
    },
    created(){
      
      //vue实例创建完毕执行的操作
      this.loadData()
    }
    
}
</script>

<!--样式-->
<style scoped>

</style>


