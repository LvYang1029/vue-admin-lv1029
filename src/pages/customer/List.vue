<template>
    <div>
        <el-button type="warning" size="small" @click="toAddHandler">添加</el-button>
        <el-button type="danger" size="small">批量删除</el-button>
        
        <el-table :data="customers">
            <el-table-column label="编号" prop="id"></el-table-column>
            <el-table-column label="姓名" prop="realname"></el-table-column>
            <el-table-column label="性别" prop="gender"></el-table-column>
            <el-table-column label="联系方式" prop="telephone"></el-table-column>
            <el-table-column label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>&nbsp;&nbsp;
                    <a href="" @click.prevent="toUpdateHandler(slot.row.id)">修改</a>
                </template>
            </el-table-column>
        </el-table>

        <!-- 分页 -->
        <br/><br/><center>
        <el-pagination 
            background 
            layout="prev, pager, next" 
            :total="1000">
        </el-pagination></center>
        <!-- 对话框 -->
        <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%">
            <!-- :before-close="handleClose" -->
            ---{{form}}
            <el-form :model="form" label-width="80px">
                <el-form-item label="用户名">
                    <el-input v-model="form.username"></el-input>
                </el-form-item>
                <el-form-item label="密码">
                    <el-input type="password" v-model="form.password"></el-input>
                </el-form-item>
                <el-form-item label="真实姓名">
                    <el-input v-model="form.realname"></el-input>
                </el-form-item>
                <el-form-item label="手机号">
                    <el-input v-model="form.telephone"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small" type="primary" @click="closeHandlerwithYES">确 定</el-button>
                <el-button @click="closeHandlerwithNO" size="small">取 消</el-button>
            </span>
        </el-dialog>
<!-- test -->
        <!-- <el-form>
            姓  名：<el-input type="text"></el-input>
            密  码：<el-input type="text"></el-input>
            验证码：<el-input type="text"></el-input>
            <el-input type="submit" value="提交"></el-input>
        </el-form> -->
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
        loadData(){
            //this为当前vue实例对象
        //vue实例创建完毕
        let url = "http://localhost:6677/customer/findAll";
        request.get(url).then((response)=>{
            //将查询结果设置到customers中,this指向外部函数的this
            this.customers = response.data;
        });
        },
        toAddHandler(){
            this.form = {
                type: "customer",
            }
            this.visible = true;
            this.title = "录入顾客信息";
        },
        closeHandlerwithYES(){
            //this.form 对象 ---字符串--> 后台{type:"customer",age:12}
            //json字符串 '{"type:"customer",age:12}'
            //request.post(url,this.form)
            //查询字符串 type=customer&age=12
            //通过request与后台进行交互，并且携带参数
            let url = "http://localhost:6677/customer/saveOrUpdate"
            request({
                url,
                method:"POST",
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response) => {
                //this.visible = false;
                //刷新
                this.loadData();
                this.closeHandlerwithNO();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        closeHandlerwithNO(){
            this.visible = false;
        },
        toUpdateHandler(){
            this.form = row;
            this.visible = true;
            this.title = "修改顾客信息";
        },
        toDeleteHandler(id){
                this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                    let url = "http://localhost:6677/customer/deleteById?id="+id
                    request.get(url).then((response)=>{
                        this.loadData();
                            this.$message({
                            type: 'success',
                            message: response.message,
                        });
                    })
                });
        }
    },
    //用于存放要向网页中显示的数据
    data(){
        return {
            title:"录入顾客信息",
            visible:false,
            customers:[],
            form:{
                type:"customer",
            }
        };
    },
    created(){
        this.loadData();
    }
}
</script>

<style scoped>
 .btn{
     background-color: #EEDDEE;
     display: inline-block;
     line-height: 3em;
     height: 3em;
     padding: 0em;
     border-radius: 3px;
     cursor: pointer;
 }
</style>