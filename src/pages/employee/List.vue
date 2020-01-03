<template>
    <div>
        <el-button size="small" type="primary" @click="toAddHandler">录入信息</el-button>
        <el-button size="small" type="warning">批量删除</el-button>
        <el-table :data="employee">
         <el-table-column fixed="left" label="编号" prop="id" ></el-table-column>
         <el-table-column label="姓名" prop="realname"></el-table-column>
         <el-table-column label="手机号" prop="telephone"></el-table-column>
         <el-table-column label="性别" prop="gender"></el-table-column>
         <el-table-column label="银行卡号" prop="bankCard"></el-table-column>
         <el-table-column label="状态" prop="type"></el-table-column>
         <el-table-column fixed="right" label="操作">
           <template v-slot="slot">
               {{slot.row.id}}
               <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
               <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
           </template>
        </el-table-column>    
        </el-table>
       <!-- 分页 -->
        <div class="block">
       <el-pagination
        layout="prev, pager, next"
        :total="50">
       </el-pagination>
        </div>
       <!-- 模态框 -->
       <el-dialog :title="title" :visible.sync="visible" width="66%" >
        <el-form :model="form" label-width="80px">
        <el-form-item label="用户名">
          <el-input v-model="form.username" />
        </el-form-item>
        <el-form-item label="密码">
          <el-input v-model="form.password" type="password" />
        </el-form-item>
        <el-form-item label="真实姓名">
          <el-input v-model="form.realname" />
        </el-form-item>
        <el-form-item label="手机号">
          <el-input v-model="form.telephone" />
        </el-form-item>
        <el-form-item label="性别">
            <el-radio v-model="form.gender" label="男">男</el-radio>
            <el-radio v-model="form.gender" label="女">女</el-radio>
        </el-form-item>
        <el-form-item label="银行卡号">
            <el-input v-model="form.bankCard"/>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
         <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
         <el-button type="warning" @click="closerModalHandler" size="small">取 消</el-button>   
         </span>
    </el-dialog>

    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {

    data(){
        return{
            visible:false,
            title:'录入员工信息',
            employee:[],
            form:{
                type:'waiter'
            }
        }

    },
    
    methods:{
         submitHandler() {
      //this.form 对象 ---字符串--> 后台 {type:'customer',age:12}
      // json字符串 '{"type":"customer","age":12}'
      // request.post(url,this.form)
      // 查询字符串 type=customer&age=12
      // 通过request与后台进行交互，并且要携带参数
      let url = "http://localhost:6677/waiter/saveOrUpdate";
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.form)
      }).then((response)=>{
        // 模态框关闭
        this.closerModalHandler();
        // 刷新
        this.loadData();
        // 提示消息
        this.$message({
          type:"success",
          message:response.message+'保存成功'
        })
      })
    },
        toDeleteHandler(id){
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          this.$message({
            type: 'success',
            message: '删除成功!'+id
          });
        })
            
        },
        loadData(){
             let url = "http://localhost:6677/waiter/findAll";
        request.get(url).then((response)=>{
            this.employee=response.data;

        })
        },
        toUpdateHandler(row){
          this.form=row,
            this.title='修改员工信息',
            this.visible=true

        },
        closerModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
          this.form = {
            type:'employee'
          }
            this.title='录入员工信息',
            this.visible=true;
        }


    },
    created(){
        //在页面加载出来时候加载数据
       this.loadData()
   }
    
 }
</script>

<style scoped>

</style>