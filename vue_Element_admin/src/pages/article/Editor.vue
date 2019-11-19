<template>
  <div>
    <el-button type="text" @click="back">返回</el-button>
    {{form}}
   <!--{{categorys}}-->
    <el-form ref="form" :model="form" label-width="80px">
       <el-form-item label="所属栏目">
        <el-select v-model="form.categoryId" placeholder="请选择所属栏目">
           <el-option v-for ="c in categorys" :key="c.id" :label="c.name" :value="c.id" ></el-option>
        </el-select>
      </el-form-item>
      <el-form-item label="文章标题">
        <el-input v-model="form.title"></el-input>
      </el-form-item>
      <el-form-item label="正文">
        <el-input type="textarea" v-model="form.content"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="onSubmit">立即创建</el-button>
        <el-button>取消</el-button>
      </el-form-item>
    </el-form>

  </div>
</template>
<script>
import request from '@/utils/request'
import qs from 'querystring'
export default {
  data(){
    return {
      form:{},
      categorys:[]
    }
  },
   
  // 页面加载完毕
  created(){
      // 进行双向数据绑定
      this.form=this.$route.query;
      this.loadCategorys();
  },

  methods:{
    loadCategorys(){
      // 调用ajax查询所有文章数据
      let url = "http://127.0.0.1:8888/article/cascadeFindAll"
      request
      .get(url)
      .then(result => {
        this.categorys = result.data;
      })
    },
    back(){
      this.$router.go(-1);
    },
    onSubmit(){
       // alert(JSON.stringify(this.form))
       // alert(qs.stringify(this.form))
       // 通过ajax将前端收集到的数据也就是this.from 发送给服务接口
       let url = "http://127.0.0.1:8888/article/saveOrUpdate ";
       request.request({
        url,
        method:"post",
        headers:{
          "Content-Type":"application/x-www-form-urlencoded"
        }, 
        data:qs.stringify(this.form)
       })
       .then(result=>{
           this.$message({
          message: result.message,
          type: 'success'
        });
        this.back();
       })
    }
  }

}
</script>
<style scoped>

</style>
