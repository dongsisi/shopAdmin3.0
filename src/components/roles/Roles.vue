<template>

  <div class="roles">
    <!-- 面包屑导航 -->
  <el-breadcrumb separator-class="el-icon-arrow-right" class="breadcrumb">
  <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
  <el-breadcrumb-item>权限管理</el-breadcrumb-item>
  <el-breadcrumb-item>角色管理</el-breadcrumb-item>
  </el-breadcrumb>
  <!-- 表格 -->
  <template>
  <el-table :data="rolesList" stripe style="width: 100%" >
    <!-- 展开行 -->
    <el-table-column type="expand">
      <template slot-scope="scope">
        <!-- 一级菜单列表 -->
        <el-row class="level1" v-for="level1 in scope.row.children" :key="level1.id">
          <el-col :span="4">
            <el-tag closable >{{level1.authName}}</el-tag>
            <i class="el-icon-arrow-right"></i>
          </el-col>
          <el-col :span="20">
            <!-- 二级菜单列表 -->
            <el-row class="level2" v-for="level2 in level1.children" :key="level2.id">
              <el-col :span="4">
                <el-tag type="success" closable >{{level2.authName}}</el-tag>
                <i class="el-icon-arrow-right"></i>
              </el-col>
              <el-col :span="20">
                <!-- 三级菜单列表 -->
                  <el-tag type="warning" closable class="level3" v-for="level3 in level2.children" :key="level3.id">{{level3.authName}}</el-tag>
              </el-col>
            </el-row>
          </el-col>
        </el-row>
      </template>
    </el-table-column>
    <!-- 自定义索引  type="index"-->
    <el-table-column  type="index"></el-table-column>
    <el-table-column prop="roleName" label="角色名称" width="180"></el-table-column>
    <el-table-column prop="roleDesc" label="描述" width="180"> </el-table-column>
    <el-table-column label="操作">
      <template slot-scope="scope">
        <el-button size="mini" type="primary" plain icon="el-icon-edit"></el-button>
        <el-button size="mini" type="danger" plain icon="el-icon-delete"></el-button>
        <el-button size="mini" type="success" plain icon="el-icon-check">分配权限</el-button>
      </template>
   </el-table-column>
  </el-table>
</template>




  </div>
</template>

<script>
export default {
   data() {
      return {
        //角色列表数据
       rolesList:[],

      }
    },
    //一进入页面，发送请求
    created(){
      this.getRolesList()
    },
  methods:{
    //获取角色列表数据
    async getRolesList(){
      const res = await this.$http.get('/roles')
      // console.log(res)
      this.rolesList = res.data.data
    }
  }
}
</script>

<style>
.breadcrumb{
  background-color: #d4dae0;
  font-size:16px;
  line-height:50px;
  padding-left:10px;
}
.level1{
  border-bottom:1px dashed #ccc;
  padding:10px 0;
}
.level1:last-child{
  border:0;
}
.level2{
  padding:5px 0;
}
.level3{
  margin:3px 3px;
}
</style>
