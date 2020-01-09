<template>
    <div>
        {{params}}
        <!-- 选项卡 -->
        <el-tabs v-model="params.status" @tab-click="loadData">
            <el-tab-pane label="全部" name="全部"></el-tab-pane>
            <el-tab-pane label="待派单" name="待派单"></el-tab-pane>
            <el-tab-pane label="待接单" name="待接单"></el-tab-pane>
            <el-tab-pane label="待服务" name="待服务"></el-tab-pane>
            <el-tab-pane label="待确认" name="待确认"></el-tab-pane>
            <el-tab-pane label="已完成" name="已完成"></el-tab-pane>
        </el-tabs>
        <!-- /选项卡结束 -->
        <!--表格-->
       <el-table :data="orders.list">
           <el-table-column prop="id" label="编号" ></el-table-column>
           <el-table-column prop="orderTime" width="200" label="下单时间" ></el-table-column>
           <el-table-column prop="total" label="总价" ></el-table-column>
           <el-table-column prop="status" label="订单状态" ></el-table-column>
           <el-table-column prop="customerId" label="顾客ID" ></el-table-column>
           <el-table-column prop="waiterId" label="员工ID" ></el-table-column>
           <el-table-column prop="addressId" label="地址ID" ></el-table-column>
           <el-table-column fixed="right" label="操作">
                <template v-slot="slot">
                    <a href="javascript:void(0)" >详情</a>
                    <a href="" v-if="slot.row.status === '待派单'" @click.prevent="toSendOrderHandler(slot.row)">派单</a>
                </template>
            </el-table-column>
       </el-table>
        <!--/表格结束-->
        <!--分页开始-->
        <el-pagination 
            :hide-on-single-page="true"
            layout="prev, pager, next" 
            :total="orders.total" 
            @current-change="pageChangeHandler">
        </el-pagination>
        <!--/分页结束-->
         <!--模态框-->
        <el-dialog title="派单"  :visible.sync="visible"  width="60%">
            
            <div>
                <p><strong>订单编号：</strong>{{form.id}}</p>
                <p><strong>订单总价：</strong>{{form.total}}</p>
                <p><strong>下单时间：</strong>{{form.orderTime}}</p>
                <p>
                    <strong>服务员工：</strong>
                    <el-radio-group v-model="waiterId">
                        <el-radio
                            border
                            v-for="e in employees"
                            :key="e.id"
                            :label="e.id"
                        >{{e.realname}}</el-radio>
                    </el-radio-group>
                </p>
            </div>
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
            //加载
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
            let url = "http://localhost:6677/order/sendOrder"
            request({
                url,
                method:"GET",
                params:{
                    orderId:this.form.id,
                    waiterId:this.waiterId
                }
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
        closeModalHandler(){
            this.visible=false;
        },
        //派单，并选择员工作为派单对象
        toSendOrderHandler(row){
            //模态框显示当前订单信息
            this.form=row;
            this.visible=true;
        },
        //加载员工信息
        loadEmployees(){
            let url = "http://localhost:6677/waiter/findAll";
                request.get(url).then(response=>{
                this.employees = response.data;
            })
        }
    },
    data(){
        return{
            title:"录入订单信息",
            visible:false,
            orders:{},
            form:{},
            params:{
                page:0,
                pageSize:10
            },
            employees:[],
            waiterId:null//选中的员工
        }
    },
    created(){
        this.loadData();
        this.loadEmployees();
    }
}
</script>
<style scoped>

</style>