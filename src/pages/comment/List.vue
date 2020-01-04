<template>
    <div>
        <!-- 按钮 -->
            <el-button size="small" type="success" @click="toAddHandler">添加</el-button>
            <el-button size="small" type="danger">批量删除</el-button>          
        <!-- /按钮结束 -->
        <!-- 表格 -->
        <el-table :data="comment">
         <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column  prop="content" label="内容"></el-table-column>            
            <el-table-column width="120" prop="commentTime" label="评论时间"></el-table-column>
            <el-table-column width="200" prop="orderId" label="序号"></el-table-column>
            <el-table-column fixed="right" label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
                </template>
            </el-table-column>
        </el-table>
        <!-- /表格结束 -->
        <!-- 分页 -->
        <el-pagination layout="prev, pager, next" :total="50">
        </el-pagination>    
        <!-- /分页结束 -->
        <!-- 横态框 -->
        <el-dialog  
        :title="title"
      :visible.sync="visible"
      width="60%">
        ---{{form}}
            <el-form :model="form" label-width="80px">

                <el-form-item label="内容">
                    <el-input type="content" v-model="form.content"/>
                </el-form-item>
                <el-form-item label="评论时间">
                    <el-input v-model="form.commentTime"/>
                </el-form-item>
                <el-form-item label="序号">
                     <el-input v-model="form.orderId"/>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="closeModalHandler" size="small">取 消</el-button>
                <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
            </span>
        </el-dialog>
        <!-- /横态框结束 -->
    </div>
</template>
<script>
import request from '@/utils/request' //自定义库
import querystring, { stringify } from 'querystring' //系统库
export default {//暴露接口
    methods:{
    loadData(){
      let url = "http://localhost:6677/comment/findAll"
      request.get(url).then((response)=>{
        // 将查询结果设置到customers中，this指向外部函数的this
        this.comment = response.data;
      })
    },
    submitHandler(){
      //this.form 对象 ---字符串--> 后台 {type:'customer',age:12}
      // json字符串 '{"type":"customer","age":12}'
      // request.post(url,this.form)
      // 查询字符串 type=customer&age=12
      // 通过request与后台进行交互，并且要携带参数
      let url = "http://localhost:6677/comment/saveOrUpdate";
      request({
        url,
        method:"POST",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        },
        data:querystring.stringify(this.form)
      }).then((response)=>{
        // 模态框关闭
        this.closeModalHandler();
        // 刷新
        this.loadData();
        // 提示消息
        this.$message({
          type:"success",
          message:response.message
        })
      })

    },
    toDeleteHandler(id){
        
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        //调用后台接口完成删除操作request
        let url="http://localhost:6677/comment/deleteById?id="+id;
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
    toUpdateHandler(row){
      this.visible = true;
      this.title="修改评论信息";
      this.form=row;

    },
    closeModalHandler(){
      this.visible = false;
    },
    toAddHandler(){
      this.visible = true;
    }
  },
  // 用于存放要向网页中显示的数据
  data(){
    return {
      visible:false,
      comment:[],
      form:{
        type:"comment"
      }
    }
  },
  created(){
    // this为当前vue实例对象
    // vue实例创建完毕 
    this.loadData()

  }
}
</script>

<style scoped>
 
</style>

    