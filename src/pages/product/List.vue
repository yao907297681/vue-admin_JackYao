<template>
    <div>
        <h5>产品管理</h5>
        <el-button type="primary" @click="toAddHandler">添加</el-button>
        <el-button type="warning">批量删除</el-button>
        <el-table :data="product">
            <el-table-column label="编号" prop="id"></el-table-column>
            <el-table-column label="产品名称" prop="name"></el-table-column>
            <el-table-column label="价格" prop="price"></el-table-column>
            <el-table-column label="描述" prop="description"></el-table-column>
            <el-table-column label="所属产品" prop="categoryId"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
               {{slot.row.id}}
               <a href="" @click.prevent="deleteHandler(slot.row.id)">删除</a>
               <a href="" @click.prevent="toUpdataHandler(slot.row)">修改</a>
               <a href="" @click.prevent="">详情</a>
                </template>
            </el-table-column>

        </el-table>
        <!-- 模态框 -->
        <el-dialog :title="title" :visible.sync="visible" width="66%" >
        <el-form :model="form" label-width="80px">
        <el-form-item label="名称">
          <el-input v-model="form.username" />
        </el-form-item>
        <el-form-item label="价格">
          <el-input v-model="form.password" />
        </el-form-item>
        <el-form-item label="所属栏目">
          <el-input v-model="form.realname" />
        </el-form-item>
        <el-form-item label="介绍">
          <el-input v-model="form.telephone" />
        </el-form-item>
        <el-form-item label="产品主图">
            <el-button size="small" type="primary">点击上传</el-button>
        </el-form-item>
      </el-form>
      <span slot="footer" class="dialog-footer">
         <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
         <el-button type="warning" @click="visible=false" size="small">取 消</el-button>   
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
            title:'产品管理',
            product:[],
            form:[]

        }
    },
   
        methods:{
             loadData(){
        let url= 'http://localhost:6677/product/findAll'
        request.get(url).then((response)=>{
            this.product=response.data

        })
        },
        toAddHandler(){
            this.title='录入产品信息',
            this.visible=true;

        },
        submitHandler(){
            let url = 'http://localhost:6677/product/saveOrUpdate'
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
                  message:response.message+'产品测试代码'
                })
            })

        },
        closerModalHandler(){
            this.visible=false;
        },
        deleteHandler(id){
            this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
              confirmButtonText: '确定',
              cancelButtonText: '取消',
              type: 'warning'
            }).then(() => { 
              let url="http://localhost:6677/product/deleteById?id="+id;
              request.get(url).then((response)=>{
                this.loadData();
                  this.$message({
                type: 'success',
                message: response.message
              });
              })
            
            });
              
        },
        toUpdataHandler(row){
          this.title='修改信息',
          this.visible=true,
          this.form=row;
        }

    //方法结束
        },
        created(){
            this.loadData()

        }
    
    
}
</script>