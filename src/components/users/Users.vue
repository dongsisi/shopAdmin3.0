<template>
<div class="users">
  <!-- 面包屑导航 -->
  <el-breadcrumb separator-class="el-icon-arrow-right" class="breadcrumb">
  <el-breadcrumb-item :to="{ path: '/' }">首页</el-breadcrumb-item>
  <el-breadcrumb-item>用户管理</el-breadcrumb-item>
  <el-breadcrumb-item>用户列表</el-breadcrumb-item>
</el-breadcrumb>
  <!-- 搜索框 -->
  <el-row :gutter="20">
    <el-col :span="8">
     <el-input placeholder="请输入搜索内容" v-model="searchText" class="input-with-select">
       <el-button slot="append" icon="el-icon-search" @click="search"></el-button>
      </el-input>
    </el-col>
    <el-col :span="4">
      <el-button type="success" plain >添加用户</el-button>
    </el-col>
</el-row>
    <!-- 表格组件： -->
    <el-table :data="userList" stripe style="width: 100%">
      <el-table-column prop="username" label="姓名" width="180"></el-table-column>
      <el-table-column prop="email" label="邮箱" width="180"></el-table-column>
      <el-table-column prop="mobile" label="电话" width="180"></el-table-column>
      <el-table-column label="用户状态">
        <template slot-scope="scope">
          <!--
            开关组件：
              v-model 用来实现数据双向绑定的
              scope.row 表示当前行数据
              mg_state 就是当前用户的状态
          -->
          <el-switch v-model="scope.row.mg_state" @change="changeUserState(scope.row)"></el-switch>
        </template>
      </el-table-column>
      <el-table-column label="操作">
        <!-- 这是 Vue 中的作用域插槽，可以通过 scope.row 来获取到当前行的数据 -->
        <template slot-scope="scope">
          <!--
            size="mini" 按钮的大小
            type="primary" 按钮的样式
            plain 是否朴素按钮（空心，镂空）
            icon="el-icon-edit" 按钮的图标
          -->
          <el-button size="mini" type="primary" plain icon="el-icon-edit"></el-button>
          <el-button size="mini" type="danger" plain icon="el-icon-delete"></el-button>
          <el-button size="mini" type="success" plain icon="el-icon-check">分配角色</el-button>
        </template>
      </el-table-column>
    </el-table>

    <!--分页组件 -->
    <el-pagination
      background
      layout="prev, pager, next"
      :total="total"
      :page-size="pagesize"
      :current-page="pagenum"
      @current-change="changePage"
    ></el-pagination>
  </div>
</template>

<script>
// 导入axios
// import axios from 'axios'
export default {
  // 进入页面，发送请求，获取数据
  created () {
    this.getUserList()
  },

  data () {
    return {
      // 用户列表数据
      userList: [],
      // 总条数：
      total: 0,
      // 当前页：
      pagenum: 1,
      // 每页大小：
      pagesize: 3,
      //搜索框
      searchText:''
    }
  },

  methods: {
    // 分页获取数据
    async getUserList (pagenum = 1, query = '') {
      const url = 'http://localhost:8888/api/private/v1/users'
      const options = {
        params: {
          // 查询条件
          query,
          // 当前页
          pagenum,
          // 每页大小
          pagesize: 3,
        }
        // 通过请求头，传递token
        // headers: {
        //   Authorization: localStorage.getItem('token')
        // }
      }

      // 使用 await 等待Promise结果
      const res = await this.$http.get(url, options)
      console.log('用户列表数据：', res)
      if (res.data.meta.status === 200) {
        // 获取数据成功
        this.userList = res.data.data.users
        // 设置总条数：
        this.total = res.data.data.total
        // 设置当前页
        this.pagenum = res.data.data.pagenum
      } else {
        // 失败
        // token失效

        // 跳回到登录页面
        this.$router.push('/login')
        // 清除token
        localStorage.removeItem('token')
      }
    },

    // 切换分页，获取当前页数据
    changePage (curPage) {
      // console.log('切换分页了：', curPage)
      this.getUserList(curPage,this.searchText)
    },

    // 切换用户状态
    async changeUserState (user) {
      try {
        // 手动抛出异常：
        // throw new Error('报错了')
        const res = await this.$http.put(
          `/users/${user.id}/state/${user.mg_state}`,
          null,
          {
            // headers: {
            //   Authorization: localStorage.getItem('token')
            // }
          }
        )

        if (res.data.meta.status === 200) {
          // 表示设置用户状态成功：
          this.$message({
            type: 'success',
            message: res.data.meta.msg,
            duration: 1000
          })
        } else {
          // 表示设置用户状态失败：
          this.$message({
            type: 'warning',
            message: res.data.meta.msg
          })
        }
      } catch (er) {
        // 上面异步操作代码出错了，才会执行 catch 中的代码逻辑
        // console.log(er)
        this.$message({
          type: 'error',
          message: '设置状态失败'
        })

        // 如果修改失败，就重置当前用户的状态
        // user.mg_state = !user.mg_state
      }
    },

    //搜索功能
    search(){
      // console.log(this.searchText)
      //默认查询第一页的用户列表
      this.getUserList(1,this.searchText);

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
</style>
