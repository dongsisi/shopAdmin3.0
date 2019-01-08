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
            <el-tag closable @close="delRights(scope.row.id, level1.id)">{{level1.authName}}</el-tag>
            <i class="el-icon-arrow-right"></i>
          </el-col>
          <el-col :span="20">
            <!-- 二级菜单列表 -->
            <el-row class="level2" v-for="level2 in level1.children" :key="level2.id">
              <el-col :span="4">
                <el-tag type="success" closable @close="delRights(scope.row.id, level2.id)">{{level2.authName}}</el-tag>
                <i class="el-icon-arrow-right"></i>
              </el-col>
              <el-col :span="20">
                <!-- 三级菜单列表 -->
                  <el-tag type="warning" closable @close="delRights(scope.row.id, level3.id)" class="level3" v-for="level3 in level2.children" :key="level3.id">{{level3.authName}}</el-tag>
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
        <el-button size="mini" type="success" plain icon="el-icon-check" @click="showRightsDialog(scope.row)">分配权限</el-button>
      </template>
   </el-table-column>
  </el-table>
</template>


<!-- 分配权限对话框 -->
<el-dialog title="角色授权" :visible.sync="isShowRightsDialog" label-width="formLabelWidth">
  <el-tree
  :data="rightsTree"
      :default-expand-all="true"
      show-checkbox
      node-key="id"
      :props="defaultProps"
      ref="tree">
</el-tree>
  <div slot="footer" class="dialog-footer">
    <el-button @click="isShowRightsDialog = false">取 消</el-button>
    <el-button type="primary" @click="assigRights">确 定</el-button>
  </div>
</el-dialog>

  </div>
</template>

<script>
export default {
   data() {
      return {
        //角色列表数据
       rolesList:[],
      //默认隐藏分配权限的对话框
      isShowRightsDialog:false,
       rightsTree: [],
         defaultProps: {
          children: 'children',
          label: 'authName'
        },
        //当前角色id
        curRoleId:-1,
      }
    },

    //一进入页面，发送请求
    created(){
      this.getRolesList()
      this.getRightsList()
    },

  methods:{
    //获取角色列表数据
    async getRolesList(){
      const res = await this.$http.get('/roles')
      // console.log(res)
      this.rolesList = res.data.data
    },
    //显示分配权限的对话框
    showRightsDialog(curRole){
      this.isShowRightsDialog = true
      //获取roleID，点击确定的时候会用到，现在此处暂存
       this.curRoleId = curRole.id

      console.log('curRole', curRole)
      const checkedKeys = []

      curRole.children.forEach(level1 => {
        // 遍历二级
        level1.children.forEach(levle2 => {
          // 遍历三级
          levle2.children.forEach(level3 => {
            checkedKeys.push(level3.id)
          })
        })
      })
      this.$nextTick(()=>{
        console.log(this.$refs.tree)
         this.$refs.tree.setCheckedKeys(checkedKeys)
      })
    },
    //分配权限功能
    async assigRights(){
      // console.log("assigRights")
      const res = await this.$http.get()
    },
    //获取分配权限的tree结构
    async getRightsList(){
      const res =await this.$http.get('/rights/tree')
      console.log(res)
      this.rightsTree = res.data.data
    },
    //分配权限（点击确定）
    async assigRights(){
      //获取到所有的节点（全选+半选） 所有被勾选中的
      const checkedKeys = this.$refs.tree.getCheckedKeys();
      const halfCheckedKeys = this.$refs.tree.getHalfCheckedKeys()
      //数组的拓展
      // 原生数组连接concat     const allkeys = checkedKeys.concat(halfCheckedKeys)
      const allKeys = [...checkedKeys,...halfCheckedKeys]
      console.log(allKeys)
      // 获取到 roleId？ =this.curRoleId
      const res = await this.$http.post(`/roles/${this.curRoleId}/rights`,{
        rids:allKeys.join(',')
      })
        // console.log(res)
        //成功后，关闭对话框
        this.isShowRightsDialog = false
        //提示信息
        this.$message({
          type:"success",
          message:res.data.meta.msg
        })
        //重新刷新列表
        this.getRolesList()
    },
    //删除角色的指定权限
    async delRights(roleId,rightId){
      const res = await this.$http.delete(`/roles/${roleId}/rights/${rightId}`)
      console.log(res)
      //成功，提示信息
      this.$message({
        type:"success",
        message:res.data.meta.msg
      })
      //刷新列表
      this.getRolesList()
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
