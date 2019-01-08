<template>
  <div class="rights">
    <!-- 面包屑导航 -->
  <el-breadcrumb separator-class="el-icon-arrow-right" class="breadcrumb">
  <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
  <el-breadcrumb-item>权限管理</el-breadcrumb-item>
  <el-breadcrumb-item>角色管理</el-breadcrumb-item>
  </el-breadcrumb>
<!-- 表格列表 -->
 <el-table :data="rightsList" stripe style="width: 100%">
    <el-table-column type="index" :index="getIndex"></el-table-column>
    <el-table-column prop="authName" label="权限名称" width="180"></el-table-column>
    <el-table-column prop="path" label="路径" width="180"></el-table-column>
    <el-table-column label="层级">
      <!-- 约定：只要不是直接展示数据中的属性值，就用作用域插槽的方法来展示数据 -->
      <template slot-scope="scope">
        <span v-if="scope.row.level==='0'">一级</span>
        <span v-else-if="scope.row.level==='1'">二级</span>
        <span v-else="scope.row.level==='2'">三级</span>
      </template>
    </el-table-column>
  </el-table>
 </div>
</template>

<script>
export default {
  data(){
    return{
       rightsList:[]
    }
  },

  created(){
    this.getRightsList()
  },


  methods:{
    //索引
    getIndex(index){
      return index
    },
    //获取权限列表
    async getRightsList(){
      const res = await this.$http.get('/rights/list')
      // console.log(res)
      this.rightsList = res.data.data
    },

  }
}
</script>

<style scoped>
.breadcrumb{
  background-color: #d4dae0;
  font-size:16px;
  line-height:50px;
  padding-left:10px;
}
</style>
