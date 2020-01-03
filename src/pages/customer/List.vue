<template>
  <div>
    <!-- sadsa -->
    <!-- 按钮 -->
    <el-button type="primary" size="small" @click="toAddHandler">添加
    </el-button>
    <el-button type="warning" size="small">批量删除 </el-button>

    <el-table :data="customers">
      //: 动态绑定讲customedrs
      <el-table-column label="编号" prop="id" />
      <el-table-column label="姓名" prop="realname" />
      <el-table-column label="手机号" prop="telephone" />
      <el-table-column label="状态" />
      <el-table-column label="操作">
        <template v-slot="slot">
          <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
          <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
        </template>
      </el-table-column>
    </el-table>
    <!-- sadsadsad -->

    <div class="block">
      <el-pagination layout="prev, pager, next" :total="50" />
    </div>
    <!-- saover -->

    <el-dialog :title="title" :visible.sync="visible" width="66%">
      <!-- <el-form :model="form" label-width="80px">
           <el-form-item label="用户名">
             <el-input v-mode="form.username">
             </el-input>
           </el-form-item>
           <el-form-item label="密码">
             <el-input v-mode="form.password" type="password">
             </el-input>
           </el-form-item>
           <el-form-item label="姓名">
             <el-input v-mode="form.realname" >
             </el-input>
           </el-form-item>
           <el-form-item label="手机号">
             <el-input v-mode="form.telephone" >
             </el-input>
           </el-form-item>
         </el-form> -->
        
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
      </el-form>
      <span slot="footer" class="dialog-footer">
        <el-button type="primary" size="small" @click="submitHandler">确 定</el-button>
        <el-button type="warning" size="small" @click="closerModalHandler">取 消</el-button>
      </span>
    </el-dialog>
  </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
  // 用于存放网页中显示的数据

  data() {
    return {
      title:'默认标题',
      visible: false,
      form: {
        type:"custome"
      },
      customers: []
    };
  },
  created() {
    // 文档加载完毕所需要的执行
  this.loadData()
  },
  // 用于存放网页中需要调用的方法
  methods: {
    loadData() {
    
    // 文档加载完毕所需要的执行
    let url = "http://localhost:6677/customer/findAll"
    //this 指vue实例 既可以访问data methods
    request.get(url).then((response) => {
      // 将查询结果设置到custos里面
      this.customers = response.data
    })
  
    },
    submitHandler() {
      //this.form 对象 ---字符串--> 后台 {type:'customer',age:12}
      // json字符串 '{"type":"customer","age":12}'
      // request.post(url,this.form)
      // 查询字符串 type=customer&age=12
      // 通过request与后台进行交互，并且要携带参数
      let url = "http://localhost:6677/customer/saveOrUpdate";
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
          message:response.message
        })
      })
    },
    toAddHandler() {
      this.form = {
        type:"customer"
      }
      this.title = '添加',
      this.visible = true
    },
    closerModalHandler() {
      this.visible = false
    },
    toUpdateHandler(row) {
    this.visible=true,
    this.form=row,
    this.title='修改个人信息'
    },
    toDeleteHandler(id) {
      // 先确认
    //   let url = 'http://localhost:6677/customer/deleteById?id=43'
    //   request.get(url).then(response => {
    //   // 将查询结果设置到custos里面
    //   this.customers = response.data
    // })

      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => { 
        let url="http://localhost:6677/customer/deleteById?id="+id;
        request.get(url).then((response)=>{
          this.loadData();
            this.$message({
          type: 'success',
          message: response.message
        });
        })
      
      });
    }
  }
};
</script>

<style scoped>
.btn {
  background-color: aqua;
  color: blue;
  display: inline-block;
  height: 3em;
  padding: 0 1em;
  cursor: pointer;
  border-radius: 3px;
}
</style>
