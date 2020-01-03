<template>
    <div>
        <!-- 按钮 -->
        <el-button type="warning" @click="toAddHandler" size="small">添加</el-button>
        <el-button type="danger"  size="small">批量删除</el-button>
        <!-- 表格 -->
        <el-table :data="employees">
            <el-table-column fixed="left" prop="id" label="编号"></el-table-column>
            <el-table-column fixed="left" prop="realname" label="姓名"></el-table-column>
            <el-table-column prop="gender" label="性别"></el-table-column>
            <el-table-column prop="telephone" label="联系方式"></el-table-column>
            <el-table-column width="200" prop="idCard" label="身份证"></el-table-column>
            <el-table-column width="200" prop="bankCard" label="银行卡"></el-table-column>
            <el-table-column fixed="right" label="操作">
                <template v-slot="slot">
                    <a href="" @click.prevent="toUpdateHandler(slot.row.id)">修改</a>&nbsp;&nbsp;
                    <a href="" @click.prevent="toDeleteHandler(slot.row.id)">删除</a>
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
        <!-- 模态框 -->
        <el-dialog
            :title="title"
            :visible.sync="visible"
            width="60%">
            <!-- :before-close="handleClose" -->
            ---{{form}}
            <el-form :model="form" label-width="80">
                <el-form-item label="用户名">
                    <el-input v-model="form.username"></el-input>
                </el-form-item>
                <el-form-item label="密码">
                    <el-input type="password" v-model="form.password"></el-input>
                </el-form-item>
                <el-form-item label="真实姓名">
                    <el-input v-model="form.realname"></el-input>
                </el-form-item>
                <el-form-item label="性别">
                    <el-radio v-model="form.gender" label="男">男</el-radio>
                    <el-radio v-model="form.gender" label="女">女</el-radio>
                </el-form-item>
                <el-form-item label="手机号">
                    <el-input v-model="form.telephone"></el-input>
                </el-form-item>
                <el-form-item label="身份证号">
                    <el-input v-model="form.idCard"></el-input>
                </el-form-item>
                <el-form-item label="银行卡号">
                    <el-input v-model="form.bankCard"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
                <el-button size="small" type="primary" @click="closeHandlerWithYES">确 定</el-button>
                <el-button @click="closeHandlerWithNO" size="small">取 消</el-button>
            </span>
        </el-dialog>
    </div>
</template>

<script>
import request from '@/utils/request'
import querystring from 'querystring'
export default {
    //用于存放网页中需要调用的方法
    methods:{
        //员工数据重载
        loadData(){
            //this -> vue实例,通过vue实例访问该实例中数据，methods
            //this.title / this.toAddHandler
            let url = "http://localhost:6677/waiter/findAll";
            request.get(url).then((response)=>{
                //箭头函数中的this指向外部函数中的this
                this.employees = response.data;
            });
        },
        toAddHandler(){
            this.visible = true;
            this.title = "录入员工信息";
        },
        closeHandlerWithYES(){
            let url = "http://localhost:6677/waiter/saveOrUpdate";
            //前端向后台发送请求，完成数据的保存操作
            request({
                url,
                method:"POST",
                //告诉给后台我的请求体中放的是查询字符串
                headers:{
                    "Content-Type":"application/x-www-form-urlencoded"
                },
                data:querystring.stringify(this.form)
            }).then((response)=>{
                this.loadData();
                this.closeHandlerwithNO();
                this.$message({
                    type:"success",
                    message:response.message
                })
            })
        },
        closeHandlerWithNO(){
            this.visible = false;
        },
        toUpdateHandler(){
            this.form = row;
            this.visible = true;
            this.title = "修改员工信息";
        },
        toDeleteHandler(){
                this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
                    confirmButtonText: '确定',
                    cancelButtonText: '取消',
                    type: 'warning'
                }).then(() => {
                this.$message({
                    type: 'success',
                    message: '删除成功!'
                });
                });
        }
    },
    //用于存放要向网页中显示的数据
    data(){
        return {
            title:"录入员工信息",
            visible:false,
        employees:[],
        form:{
            type:"waiter"
        }
        };
    },
    created(){
        //在页面加载出来的时候加载数据
        this.loadData();
    }
}
</script>

<style scoped>
  .btn{
      background-color: #EEDDEE;
      display: inline-block;
      height: 3em;
      border-radius: 3em;
      cursor: pointer;
  }
</style>