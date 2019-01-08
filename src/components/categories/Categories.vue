<template>
  <div class="categories">
    <!-- 添加分类组件 -->
    <el-button type="success" plain @click="showAddCateDialog">添加分类</el-button>
    <!-- 表格组件 -->
    <el-table :data="cateList" stripe style="width: 100%" v-loading="loading">
    <el-table-tree-column prop="cat_name" label="分类名称" width="220" tree-key="cat_id" level-key="cat_level" parent-key="cat_pid" :indent-size="30"></el-table-tree-column>
    <el-table-column label="是否有效" width="180">
      <template slot-scope="scope">
        {{scope.row.cat_deleted ? "否":"是"}}
      </template>
    </el-table-column>
    <el-table-column prop="cat_level" label="层级"></el-table-column>
    <el-table-column label="操作">
      <template slot-scope="scope">
        <el-button size="mini" type="primary" plain icon="el-icon-edit"></el-button>
        <el-button size="mini" type="danger" plain icon="el-icon-delete"></el-button>
      </template>
    </el-table-column>
  </el-table>
   <!-- 分页组件： -->
  <el-pagination
    background
    layout="prev, pager, next"
    :total="total"
    :page-size="pagesize"
    :current-page="pagenum"
    @current-change="changePage"
  >
  </el-pagination>
  <!-- 添加分类对话框组件 -->
  <el-dialog title="添加分类" :visible.sync="isShowAddCateDialog">
  <el-form :model="AddCateForm" label-width="100px">
    <el-form-item label="分类名称" >
      <el-input v-model="AddCateForm.cat_name" autocomplete="off"></el-input>
    </el-form-item>
    <el-form-item label="父级名称" >
      <el-cascader  v-model="AddCateForm.catPidArr"
          :options="AddCateList" change-on-select
          :props="{
            label: 'cat_name',
            value: 'cat_id'
          }"></el-cascader>
    </el-form-item>
  </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button @click="isShowAddCateDialog = false">取 消</el-button>
    <el-button type="primary" @click="addCate">确 定</el-button>
  </div>
</el-dialog>
  </div>
</template>

<script>
export default {
  data(){
    return{
      cateList:[],
      // 总条数
      total: 0,
      // 当前页
      pagenum: 0,
      // 每页大小
      pagesize: 5,
      //加载中效果
      loading:false,
      //添加分类数据
      AddCateForm:{
        //级联选择器的数据
        catPidArr:[],
        cat_name:''
      },
      //添加分类展示与隐藏
      isShowAddCateDialog:false,
      AddCateList:[],
    }
  },
  created(){
    this.getCateList()
    this.getAddCateList()
  },
  methods:{
    async getCateList(curPage=1){
      this.loading = true
      const res = await this.$http.get('/categories',{
        params:{
           type: 3,
          pagenum: curPage,
          pagesize: 5
        }
      })
      console.log(res)
     const {result, pagenum, total}= res.data.data
     this.cateList = result
     this.pagenum = pagenum+1
     this.total = total
     this.loading = false
    },
    //切换分页
     changePage (curPage) {
      this.getCateList(curPage)
    },
    //展示添加分类对话框
    showAddCateDialog(){
      this.isShowAddCateDialog = true
    },
    //添加分类列表（获取级联选择器数据一级二级菜单数据）功能
    async getAddCateList(){
      // console.log("ee")
      const res = await this.$http.get('/categories',{
        params:{
          type:2
        }
      })
      this.AddCateList = res.data.data
    },
    //添加分类
    async addCate(){
      const {cat_name,catPidArr} = this.AddCateForm
      // console.log(cat_name, catPidArr[catPidArr.length - 1], catPidArr.length)

      const res = await this.$http.post('/categories',{
        cat_pid:catPidArr[catPidArr.length-1],
        cat_name,
        cat_level:catPidArr.length,
      })
      // console.log(res)
      //关闭对话框
      this.isShowAddCateDialog = false
      //提示消息
      this.$message({
        type:"success",
        message:res.data.meta.msg
      })
      //刷新列表
      this.getCateList()
    }

  }
}
</script>

<style>

</style>
