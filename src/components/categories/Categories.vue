<template>
  <div class="categories">
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
  <!-- 分页组件 -->
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
      loading:false
    }
  },
  created(){
    this.getCateList()
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
    }
  }
}
</script>

<style>

</style>
