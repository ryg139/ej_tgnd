<template>
    <div>
    <!--选项卡-->
        <el-tabs v-model="params.status" @tab-click="loadData" >
            <el-tab-pane label="待派单" name="待派单"></el-tab-pane>
            <el-tab-pane label="配置管理" name="second">配置管理</el-tab-pane>
            <el-tab-pane label="角色管理" name="third">角色管理</el-tab-pane>
            <el-tab-pane label="定时任务补偿" name="fourth">定时任务补偿</el-tab-pane>
        </el-tabs>
        <!--/按钮结束-->
        <!--表格-->
       <el-table :data="orders.list">
           <el-table-column prop="id" label="编号" ></el-table-column>
           <el-table-column prop="orderTime" width="200" label="下单时间" ></el-table-column>
           <el-table-column prop="total" label="总价" ></el-table-column>
           <el-table-column prop="status" label="订单状态" ></el-table-column>
           <el-table-column prop="customerId" label="顾客ID" ></el-table-column>
           <el-table-column prop="waiterId" label="员工ID" ></el-table-column>
           <el-table-column prop="addressId" label="地址ID" ></el-table-column>
           <el-table-column fixed="right" label="操作" >
               <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">详情</a>
                    <a href="" v-if="slot-row.status==='待派单'" @click.prevent="toSandOrderHandler(slot.row)">派单</a>
               </template>
           </el-table-column>
       </el-table>
        <!--/表格结束-->
        <!--分页开始-->
        <el-pagination 
            hide-on-single-page
            layout="prev, pager, next" 
            :total="orders.total" 
            @current-change="pageChangeHandler">
        </el-pagination>
        <!--/分页结束-->
         <!--模态框-->
        <el-dialog :title="title"  :visible.sync="visible"  width="60%">
            {{form}}
            <el-form :model="form" lable-width="80px">
                <el-form-item label="订单编号">
                    <el-input v-model="form.orderTime"></el-input>
                </el-form-item>
                <el-form-item label="订单状态">
                    <el-input v-model="form.status" ></el-input>
                </el-form-item>
                <el-form-item label="顾客编号">
                    <el-input v-model="form.customerId" ></el-input>
                </el-form-item>
                <el-form-item label="员工编号">
                    <el-input v-model="form.waiterId" ></el-input>
                </el-form-item>
                <el-form-item label="地址编号">
                    <el-input v-model="form.addressId" ></el-input>
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
        //当分页中当前页改变的时候执行
        pageChangeHandler(page){
            //将params中当前页改为插件中的当前页
            this.params.page = page-1;
            //刷新页面
            this.loadData();
        },
        loadData(){
            let url = "http://localhost:6677/order/queryPage"
            if(this.params.status==="全部"){
                delete this.params.status;
            }
            request({
                url,
                method:"post",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.params)
            }).then((response)=>{
                //orders为一个对象
                this.orders = response.data;
            })
        },
        submitHandler(){
            let url = "http://localhost:6677/order/save"
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
                let url="http://localhost:6677/order/deleteById?id="+id;
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
            this.title="修改订单信息";
            this.visible=true;
        },
        closeModalHandler(){
            this.visible=false;
        },
        toAddHandler(){
            this.form={
                type:"order"
            }
            this.title="录入订单信息";
            this.visible=true;
        }
    },
    data(){
        return{
            title:"录入订单信息",
            visible:false,
            orders:{},
            form:{
                type:"order"
            },
            params:{
                page:0,
                pageSize:10
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