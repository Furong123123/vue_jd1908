<template>
  <div>
    <div class="btns">
      <el-button type="primary" size="small" @click="toPublishArticle">发布资讯</el-button>
    </div>
    <el-table :data="articles">
      <el-table-column prop="id" label="编号"></el-table-column>
      <el-table-column prop="title" label="标题"></el-table-column>
      <el-table-column prop="authorId" label="作者"></el-table-column>
      <el-table-column prop="category.name" label="栏目"></el-table-column>
      <el-table-column label="操作" fixed="right" width="250" align="center">
      <template slot-scope="scope">
         <el-button
          size="mini"
          @click="handleRevice( scope.row)">查看</el-button>
        <el-button
          size="mini"
          @click="handleEdit( scope.row)">编辑</el-button>
        <el-button
          size="mini"
          type="danger"
          @click="toDelete(scope.row.id)">删除</el-button>
      </template>
    </el-table-column>

    </el-table>
  </div>
</template>

<script>
import request from '@/utils/request'
export default {
  // 为模板提供数据
  data() {
    return {
      articles: []
    }
  },
  // 生命周期
  created() {
   this.reloadDate();
  },

  // 方法，处理事件函数
  methods:{
    // 重载数据
    reloadDate(){
       // 调用ajax查询所有的文章数据
    let url = "http://127.0.0.1:8888/article/cascadeFindAll"
    request.get(url).then(result => {
      //console.log(this.data)
       this.articles = result.data
    })
    },
    toPublishArticle(){
      // 控制跳转
      this.$router.push({path:'/article/Editor'})
    },
    handleRevice(){
    },
    handleEdit(record){
      // console.log(record);
       this.$router.push({path:'/article/Editor',query:record})
    },
   toDelete(id){
      this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      }).then(() => {
        //  与后台交互
        let url ="http://127.0.0.1:8888/article/deleteById"
        request.get(url,{params:{id}})
        .then(response =>{
          // 通知
          this.$message({
            message:response.message,
            type:"success"
          })
          // 重载数据
          this.reloadDate()
        })
      })
    }
  }
}
</script>
<style scoped>

</style>
