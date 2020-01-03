<template>
    <div>
        <!--按钮 -->
       <el-button type="primary" size="small" @click="toAddHandler"> 添加</el-button>
         <el-button type="primary"  size="small">批量删除</el-button>
         <!-- 按钮结束 -->
<!-- 表格 -->
         <el-table :data="employees">
             <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
             <el-table-column fixed="left" prop="username" label="用户名"></el-table-column>
             <el-table-column fixed="left" prop="realname" label="姓名"></el-table-column>
             <el-table-column prop="gender" label="性别"></el-table-column>
             <el-table-column width="120" prop="telephone" label="手机号"></el-table-column>
             <el-table-column width="500" prop="idCard" label="身份证号"></el-table-column>
              <el-table-column width="500" prop="bankCard" label="银行卡号"></el-table-column>
               <el-table-column fixed="right" label="操作">
                   <template v-slot="slot">
                        <a href="" @click.prevent="toDeleteHander(slot.row.id)">删除</a>
                        <a href="" @click.prevent="toUpadataHander(slot.row)">修改</a>   
                   </template>
               </el-table-column>
             
         </el-table>
 <!-- 表格结束 -->
 <!-- 分页 -->
      <el-pagination
    layout="prev, pager, next"
    :total="50">
  </el-pagination>
  <!--/分页-->
   <!--模态框-->
  <el-dialog
  :title="title"
  :visible.sync="visible"
  width="60%"
 >
 测试：{{form}}
 <el-form :model="form" lablel-width="80px">
   <el-form-item label="用户名">
     <el-input v-model="form.username">
     </el-input>
   </el-form-item>
   <el-form-item label="密码">
     <el-input type="password" v-model="form.password">
     </el-input>
   </el-form-item>
   <el-form-item label="性名" >
     <el-input v-model="form.realname">
     </el-input>
   </el-form-item>
   <el-form-item label="性别">
     <el-radio-group v-model="form.gender">
    <el-radio label="男">男</el-radio>
    <el-radio label="女">女</el-radio>
  </el-radio-group>
   </el-form-item>
   <el-form-item label="手机号">
     <el-input v-model="form.telephone">
     </el-input>
   </el-form-item>
   <el-form-item label="身份证号码">
     <el-input v-model="form.idCard">
     </el-input>
   </el-form-item>
   <el-form-item label="银行卡号">
     <el-input v-model="form.bankCard">
     </el-input>
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

<script>
import request from '@/utils/request'
import querystring from 'querystring' 
export default {
    data(){
        return{
            title:"录入员工信息",
            visible:false,
            employees:[],
            form:{
              type:"waiter"
            }
        }
    },
    created(){
      //在页面加载出来的时候加载数据
      this.loadData();
    },
   methods:{
      loadData(){
        let url ="http://localhost:6677/waiter/findAll"
      request.get(url).then((response)=>{
        //将查询结果设置到customer中,this指向外部函数的this
         this.employees = response.data;
      })
      },

      submitHander(){
//调用request与后台进行交互，并且携带参数
//this.form对象---字符串-->后台
let url = "http://localhost:6677/waiter/saveOrUpdate"
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
          this.$message({
            type: 'success',
            message: '删除成功!' +   "编号为："+id
          });
        })
          },
      
        toUpadataHander(row){
          this.title = "修改员工信息";
            this.visible = true;
        },
        closeModalHandler(){
       this.visible = false;
        },

 
        toAddHandler(){
          this.title = "录入员工信息";
            this.visible = true;

        }

    }
}
</script>


<style scoped>

</style>