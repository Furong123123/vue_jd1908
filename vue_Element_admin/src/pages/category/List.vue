<template>
  <div>
      <!--按钮区-->
    <div class="btns">
      <el-button type="primary" size="small" @click="toAdd">新增栏目</el-button>
      <el-button type="danger" size="small" @click="toBathDelete">批量删除</el-button>

    </div>
    <!--表格区域-->
    {{ids}}
    <el-table :data="categorys"  @selection-change="handleSelectionChange">
       <el-table-column type="selection" width="55"> </el-table-column>
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="name" label="栏目名称"></el-table-column>
      <el-table-column prop="description" label="栏目描述"></el-table-column>
      <el-table-column prop="parentId" label="父栏目"></el-table-column>
      <el-table-column label="操作" fixed="right" width="250" align="center">
      <template slot-scope="scope">
        <el-button
          size="mini"
          type="text"
          @click="handleEdit( scope.row)">编辑</el-button>
        <el-button
          size="mini"
          type="danger"
          @click="handleDelete(scope.row.id)">删除</el-button>
      </template>
    </el-table-column>

    </el-table>
    <!--模态框-->

<el-dialog title="添加栏目信息" :visible.sync="dialogFormVisible">
 {{form}} 
  <el-form :model="form">
    <el-form-item label="栏目名称" label-width="80px">
      <el-input v-model="form.name" autocomplete="off"></el-input>
    </el-form-item>
      <el-form-item label="所属栏目" label-width="80px">
      <el-select v-model="form.parentId" clearable placeholder="请选择所属栏目">
      <el-option v-for ="c in categorys" :key="c.id" :label="c.name" :value="c.id" ></el-option>
    </el-select>
    </el-form-item>
    <el-form-item label="栏目描述" label-width="80px">
      <el-input v-model="form.description"  type="textarea" autocomplete="off"></el-input>
    </el-form-item>
  </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button  size="small" @click="dialogFormVisible = false">取 消</el-button>
    <el-button type="primary"  size="small"  @click="submitHandler">确 定</el-button>
  </div>
</el-dialog>
  </div>
</template>

<script>
import request from '@/utils/request'
import  qs from "querystring"
export default {
  // 为模板提供数据
  data() {
    return {
     dialogFormVisible:false,
      form:{},
      categorys: [],
      ids:[]
     

    }
  },
  // 生命周期
  created() {
    this.reloadData();
  },

  // 方法，处理事件函数
  methods:{
    submitHandler(){
      request.request({
        url:'http://127.0.0.1:8888/category/saveOrUpdate',
        method:'post',
        headers:{
          'Content-Type':'application/x-www-form-urlencoded'
        },
        data:qs.stringify(this.form)
      })
      .then(response=>{
        // 提示信息
        this.$message({
          message:response.message,
          type:"success"
        })
        // 关闭模态框
        this.dialogFormVisible = false;
        // 重载数据
        this.reloadData();
        
      })
    },
    handleSelectionChange(val){
      // 遍历，拿出里面的id的值
        this.ids = val.map(item=>item.id);
    },
    reloadData(){
      // 调用ajax查询所有文章数据
      let url = "http://127.0.0.1:8888/category/findAll"
      request
      .get(url)
      .then(result => {
        this.categorys = result.data;
      })
    },

    toAdd(){
      this.form={},
     //  弹出模态框
     this.dialogFormVisible=true;
    },
    handleEdit(record){
      this.dialogFormVisible=true;
      // 为from绑定要修改的值
      this.form = record;
    },
    toBathDelete(){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
      .then(() =>{
        let url = "http://127.0.0.1:8888/category/batchDelete"
        request.request({
          url,
          method:"post",
          headers:{
            'Content-Type':'application/x-www-form-urlencoded'
          },
          data:qs.stringify({ids:this.ids})
        })
        .then(response=>{
          this.$message({ type: 'success', message: response.message });
          // 重载数据
          this.reloadData();
        })
      })
    },
   handleDelete(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        //  与后台交互
        let url ="http://127.0.0.1:8888/category/deleteById"
        request.get(url,{params:{id}})
        .then(response =>{
          // 通知
          this.$message({
            message:response.message,
            type:"success"
          })
          // 重载数据
           this.reloadData();
        })
        })
    }
  }
}
</script>
<style scoped>

</style>
