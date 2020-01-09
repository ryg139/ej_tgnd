<template>
    <div>
        <!-- 按钮 -->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">删除</el-button>
        
        <!-- /按钮 -->
        <!-- 表格 -->
        <el-table :data="products">
            
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="产品名称"></el-table-column>
            <el-table-column prop="price" label="价格"></el-table-column>
            <el-table-column prop="description" width="200" label="描述"></el-table-column>
            <el-table-column prop="categoryId" label="所属产品"></el-table-column>
            <el-table-column label="产品图片">
              <template slot-scope="scope">
                <img :src="scope.row.photo" width="20" height="20">
              </template>
            </el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">
                      <i class="el-icon-delete"/>
                    </a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">
                      <i class="el-icon-edit"/>
                    </a>
                </template></el-table-column>
            
        </el-table>
        <!-- /表格 -->
        <!-- 分页 -->
        <!-- <el-pagination layout="prev, pager, next" :total="50"></el-pagination> -->
        <!-- /分页 -->
        <!-- 模态框 -->
        <el-dialog
        title="录入产品信息"
        :visible.sync="visible"
        width="60%">
        <el-form :model="form" label-width="80px">

            
            <el-form-item label="产品名称">
                <el-input v-model="form.name"/>
            </el-form-item>
            <el-form-item label="价格">
                <el-input v-model="form.price"/>
            </el-form-item>
            <el-form-item label="所属产品">
              <el-select v-model="form.categoryId">
                <el-option v-for="item in options" :key="item.id" :label="item.name" :value="item.id">
                </el-option>
              </el-select>
            </el-form-item>
            <el-form-item label="描述">
              <el-input  type="textarea" v-model="form.description"/>
            </el-form-item>
            <el-form-item label="图片">
              <el-upload
                class="upload-demo"
                action="http://134.175.154.93:6677/file/upload"
                :on-success="uploadSuccessHandler"
                :file-list="fileList"
                list-type="picture">
                <el-button size="small" type="primary">点击上传</el-button>
                <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
              </el-upload>
            </el-form-item>
        </el-form>
      
      <span slot="footer" class="dialog-footer">
        <el-button size="small" @click="closeModalHandler">取 消</el-button>
        <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
      </span>
    </el-dialog>
        <!-- /模态框 -->
    </div>
</template>
<script>
import request from'@/utils/request'
import querystring from 'querystring'
export default {
  // 用于存放网页中需要调用的方法
  created(){
          //vue实例创建完毕
          this.loadData();
          this.loadCategory();
        },
  methods:{
    uploadSuccessHandler(response){
      let photo="http://134.175.154.93:8888/group1/"
      +response.data.id
      this.form.photo=photo;
    },
    loadCategory(){
      let url = "http://localhost:6677/category/findAll"
      request.get(url).then((response)=>{
      this.options = response.data;
    })
    },
    loadData(){
      let url = "http://localhost:6677/product/findAll"
      request.get(url).then((response)=>{
      //将查询结果设置到product中
      this.products = response.data;
    })
    },
    submitHandler(){
           let url = "http://localhost:6677/product/saveOrUpdate";
           request({
             url,
             method:"post",
             headers:{
               "Content-Type":"application/x-www-form-urlencoded"
             },
              data:querystring.stringify(this.form)
      }).then((response)=>{
                //模态框关闭
                this.closeModalHandler();
                //刷新
                this.loadData();
                //关闭模态框
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
          //调用后台接口
          let url = "http://localhost:6677/product/deleteById?id="+id;
          request.get(url).then((response)=>{
            //刷新数据  提示结果
            this.loadData();
              this.$message({
              type: 'success',
              message:"删除成功"
            });

          })
          
        })
        
      },
      toUpdateHandler(row){
        //模态框表单中显示出当前行信息
        this.form = row;
        //打开模态框
        this.visible = true;
        this.fileList=[];
      },
      closeModalHandler(){
        this.visible = false;
      },
      toAddHandler(){
        this.visible = true;
        this.fileList=[];
        this.form={
        }
      }
    },
  // 用于存放要向网页中显示的数据
      data(){
        return {
          visible:false,
          products:[],
          options:[],
          fileList:[],
          form:{
          }
        }
      },
        
    }
</script>
<style scoped>


</style>