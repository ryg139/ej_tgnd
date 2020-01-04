<template>
    <div>
    <!--按钮-->
       <el-button type="success" size="small" @click="toAddHandler">添加</el-button>
       <el-button type="danger" size="small" >批量删除</el-button>
        <!--/按钮结束-->
        <!--表格-->
       <el-table :data="customers">
           <el-table-column prop="id" label="编号" ></el-table-column>
           <el-table-column prop="realname" label="姓名" ></el-table-column>
           <el-table-column prop="telephone" label="联系方式" ></el-table-column>
           <el-table-column label="操作" >
               <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
                    <a href="" @click.prevent="toUpdateHandler(slot.row)">修改</a>
               </template>
           </el-table-column>
       </el-table>
        <!--/表格结束-->
        <!--分页开始-->
        <!-- <el-pagination layout="prev, pager, next" :total="50">
        </el-pagination> -->
        <!--/分页结束-->
         <!--模态框-->
        <el-dialog :title="title"  :visible.sync="visible"  width="60%">
            <!-- 测试：{{form}} -->
            <el-form :model="form" lable-width="80px">
                <el-form-item label="用户名">
                    <el-input v-model="form.username"></el-input>
                </el-form-item>
                <el-form-item label="密码">
                    <el-input v-model="form.password" type="password"></el-input>
                </el-form-item>
                <el-form-item label="真实姓名">
                    <el-input v-model="form.realname" ></el-input>
                </el-form-item>
                <el-form-item label="手机号">
                    <el-input v-model="form.telephone" ></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button @click="closeModalHandler" size="small">取 消</el-button>
                <el-button type="primary" @click="submitHandler" size="small">确 定</el-button>
            </span>
        </el-dialog>
         <!--/模态框结束-->
    </div>
</template>
<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    methods:{
        loadData(){
            let url = "http://localhost:6677/customer/findAll"
            request.get(url).then((response)=>{
                this.customers = response.data;
            })
        },
        submitHandler(){
            let url = "http://localhost:6677/customer/saveOrUpdate"
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                //请求结束，关闭模态框
                this.closeModalHandler();
                //刷新
                this.loadData();
                //提示信息
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
                let url="http://localhost:6677/customer/deleteById?id="+id;//传参
                request.get(url).then((response)=>{
                    //刷新
                    this.loadData();
                    //提示
                    this.$message({
                        type: 'success',
                        message: response.message
                    });
                })
                
            })
        },
        toUpdateHandler(row){
            this.form=row;
            this.title="修改顾客信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.form={
                type:"customer"
            }
            this.title="录入顾客信息";
            this.visible=true;
        }
    },
    data(){
        return{
            title:"录入顾客信息",
            visible:false,
            customers:[],
            form:{
                type:"customer"
            }
        }
    },
    created(){
        this.loadData();
    }
}
</script>
<style scoped>

</style>