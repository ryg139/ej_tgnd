<template>
    <div>
        <!-- 按钮 -->
        <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">删除</el-button>
        <!-- /按钮 -->
        <!-- 表格 -->
        <el-table :data="employees">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="username" label="用户名"></el-table-column>
            <el-table-column prop="realname" label="姓名"></el-table-column>
            <el-table-column prop="gender" label="性别"></el-table-column>
            <el-table-column width="120" prop="telephone" label="联系方式"></el-table-column>
            <el-table-column width="200" prop="idCard" label="身份证号"></el-table-column>
            <el-table-column width="200" prop="bankCard" label="银行卡号"></el-table-column>
            <el-table-column fixed="right" label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler">修改</a>
                </template></el-table-column>
        </el-table>
        <!-- /表格 -->
        <!-- 分页 -->
        <el-pagination layout="prev, pager, next" :total="50"></el-pagination>
        <!-- /分页 -->
        <!-- 模态框 -->
        <el-dialog
        title="录入顾客信息"
        :visible.sync="visible"
        width="60%">
        <el-form :model="form" label-width="80px">
            <el-form-item label="用户名">
                <el-input v-model="form.username"/>
            </el-form-item>
            <el-form-item label="密码">
                <el-input type="password" v-model="form.password"/>
            </el-form-item>
            <el-form-item label="姓名">
                <el-input v-model="form.realname"/>
            </el-form-item>
            <el-form-item label="性别">
                <el-radio-group v-model="form.gender">
                    <el-radio label="男">男</el-radio>
                    <el-radio label="女">女</el-radio>
                    
                </el-radio-group>
            </el-form-item>

            <el-form-item label="手机号">
              <el-input v-model="form.telephone"/>
            </el-form-item>

            <el-form-item label="银行卡号">
              <el-input v-model="form.bankCard"/>
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
export default {//暴露接口
    data(){
        return{
            title:"添加员工信息",
            visible:false,
            employees:[],
            form:{
                type:"waiter"
            }
        }
    },
    created(){
        //在页面加载出来时加载数据
        this.loadData();
    },
    methods:{
        submitHandler(){
           let url = "http://localhost:6677/waiter/saveOrUpdate";
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

        loadData(){
            //this ->vue实例，通过vue实例访问该实例中数据
            //this.title/this.toAddhandler
            let url = "http://localhost:6677/waiter/findAll"
            request.get(url).then((response)=>{
                this.employees = response.data; 
            }) 
        },
        
        toAddHandler(){
            this.visible=true
            this.title="录入员工信息"
        },
        closeModalHandler(){
            this.visible=false
        },
        toDeleteHandler(){
            this,visible=true
            this.title="删除员工信息"
        },
        toUpdateHandler(){
            this.visible=true
        }
    }
}
</script>
<style scoped>


</style>