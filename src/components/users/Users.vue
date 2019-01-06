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
      <el-button type="success" plain @click="showUserAddDialog">添加用户</el-button>
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
          <el-button size="mini" type="primary" plain icon="el-icon-edit"></el-button>
          <el-button size="mini" type="danger" plain icon="el-icon-delete" @click="delUserById(scope.row.id)"></el-button>
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
    <!-- 添加用户对话框 -->
<el-dialog title="添加用户" :visible.sync="isShowUserAddDialog" @close="hideUserAddDialog">
  <el-form :model="userAddForm" label-width="100px" :rules="rules" ref="userAddFormRef" >
    <el-form-item label="用户名" prop="username">
      <el-input v-model="userAddForm.username" autocomplete="off"></el-input>
    </el-form-item>
    <el-form-item label="密码" prop="password">
      <el-input v-model="userAddForm.password" autocomplete="off"></el-input>
    </el-form-item>
     <el-form-item label="邮箱" prop="email">
      <el-input v-model="userAddForm.email" autocomplete="off"></el-input>
    </el-form-item>
     <el-form-item label="手机号" prop="mobile">
      <el-input v-model="userAddForm.mobile" autocomplete="off"></el-input>
    </el-form-item>
  </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button @click="isShowUserAddDialog = false">取 消</el-button>
    <el-button type="primary" @click="addUser">确 定</el-button>
  </div>
</el-dialog>
    <!-- 编辑用户对话框 -->
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
      searchText:'',
      //展示与显示添加用户对话框false为隐藏
      isShowUserAddDialog:false,
      //添加用户数据
      userAddForm:{
        username:'',
        password:'',
        email:'',
        mobile:''
      },
      //添加用户的表单验证
       rules: {
        username: [
          { required: true, message: '请输入用户名', trigger: 'blur' },
          { min: 5, max: 12, message: '用户名长度为5到12个字符', trigger: 'blur'}
        ],
        password: [
          { required: true, message: '请输入密码', trigger: 'blur' },
          { min: 6, max: 12, message: '密码长度为6到12个字符', trigger: 'blur' }
        ],
        email: [   // 通过 pattern 来指定一个正则表达式来对表单进行验证
          {
            pattern: /^([a-zA-Z0-9]+[_|_|.]?)*[a-zA-Z0-9]+@([a-zA-Z0-9]+[_|_|.]?)*[a-zA-Z0-9]+\.[a-zA-Z]{2,3}$/,
            message: '邮箱格式不正确',
            trigger: 'blur'
          }
        ],
        mobile: [
          {
            pattern: /^1(3|4|5|7|8)\d{9}$/,
            message: '手机号码格式不正确',
            trigger: 'blur'
          }
        ]
      },
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
    },
    //删除功能
    async delUserById (id) {
      // 弹出确认对话框
      try{
        await this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
        confirmButtonText: '确定',
        cancelButtonText: '取消',
        type: 'warning'
      })
      //发送请求，删除数据
        const res = await this.$http.delete(`/users/${id}`)
      // console.log(res)
          if(res.data.meta.status===200){
            this.$message({
              type:'success',
              message:res.data.meta.msg
            })
            //刷新用户列表
            this.getUserList(1,this.searchText)
          }else{
            this.$message({
              type:'warning',
              message:res.data.meta.msg
            })
          }
          }catch(err){
          //取消删除
          this.$message({
            type:"info",
            message:'取消删除'
          })
          }
    },
    //展示添加用户的对话框(显示和隐藏true或false)
    showUserAddDialog(){
      this.isShowUserAddDialog = true
    },
    //添加用户功能
    async addUser(){
      // console.log("addUser")
      // console.log(this.userAddForm)  添加的用户的所有信息
      try{
        //1.表单验证
        await this.$refs.userAddFormRef.validate()
        //2.添加用户代码逻辑
        const res = await this.$http.post('/users', this.userAddForm)
        // console.log(res)
         //2.1判段状态码，
         if(res.data.meta.status===201){
           //2.2关闭消息框
           this.isShowUserAddDialog = false
           //2.2提示用户信息
           this.$message({
             type:'success',
             message:res.data.meta.msg
           })
           //2.3重新刷新列表信息
          this.getUserList(1,this.searchText)
         }
      }catch(err){
        //3.表单验证失败
      }
    },
    //隐藏添加用户的对话框
    hideUserAddDialog(){
      //重置表单
      this.$refs.userAddFormRef.resetFields()
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
