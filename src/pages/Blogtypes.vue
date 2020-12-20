<template>
  <div class="table">
    <div class="pagetitle">博客类型管理</div>
    <div class="handle-box">
      <el-button type="primary" size="mini" @click="centerDialogVisible = true"
        >添加博客类型</el-button
      >
    </div>

        <!-- 前端展示所有的博客类型 -->
        <el-table size="mini" ref="multipleTable" border style="width:100%" height="680px" :data="data">
            <el-table-column prop="name" label="类型名称" width="120" align="center"></el-table-column>
            <el-table-column label="简介">
                <template slot-scope="scope">
                    <article class="markdown-body" v-html="scope.row.desc" ></article>
                </template>
            </el-table-column>
        </el-table>

        <!-- 新增框 -->
        <el-dialog title="添加类型" :visible.sync="centerDialogVisible" width="100%" center>
            <el-form :model="registerForm" ref="registerForm" label-width="80px">
                <el-form-item prop="name" label="名称" size="mini">
                    <el-input v-model="registerForm.name" placeholder="类型名称"></el-input>
                </el-form-item>
                <el-form-item prop="desc" label="描述" size="mini">
                    <mavon-editor v-model="registerForm.desc" ref="md" @change="change" />
                </el-form-item>         
            </el-form>
            <span slot="footer">
                <el-button size="mini" @click="centerDialogVisible = false">取消</el-button>
                <el-button size="mini" @click="addBlogType">确定</el-button>                
            </span>
        </el-dialog>
  </div>
</template>


<script>
import {setBlogType,getBlogType} from '../api/index';
import { mixin } from '../mixins/index';
export default {
    mixins: [mixin],
    data(){
        return{
            test:"<h3>三个标题</h3>",
            centerDialogVisible:false,
            registerForm:{   // 添加框
                name:'',
                desc:''
            },
            htmll:'',
            tableData: [],
            tempData: [],
        }
    },
    created(){
        this.getData();
    },
    computed:{
        //
        data(){
            return this.tableData;
        }
    },
    methods:{
        // 获取全部博客类型
        getData(){
            this.tempData = [];
            this.tableData = [];
            getBlogType().then(res => {
                this.tempData = res;
                this.tableData = res;
                this.currentPage = 1;
            })
        },
        // 添加博客类型
        addBlogType(){
            let params = new URLSearchParams();
            params.append('name',this.registerForm.name);
            params.append('descc',this.htmll);
            setBlogType(params)
            .then(res => {
                if(res){
                    this.getData();
                    this.notify("添加成功","success");
                }else{
                    this.notify("添加失败","error");
                }
            })
            .catch(err => {
                console.log(err);
            });
            this.centerDialogVisible = false;
        },
        // 所有操作都会被解析重新渲染
        change(value, render){
            // render 为 markdown 解析后的结果[html]
            this.htmll = render;
        },
    }
};
</script>


<style>
.pagetitle {
  margin-top: 50px;
}
</style>