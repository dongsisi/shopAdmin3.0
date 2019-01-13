<template>
  <div class="goods">
    <!-- 添加商品按钮 -->
    <el-button type="success" plain @click="$router.push('/goods-add')">添加商品</el-button>
   <!-- 表格组件 -->
   <el-table :data="goodsList" stripe style="width: 100%">
     <el-table-column type="index"></el-table-column>
    <el-table-column prop="goods_name" label="商品名称" width="180">
    </el-table-column>
    <el-table-column prop="goods_price" label="商品价格" width="180">
    </el-table-column>
    <el-table-column prop="goods_weight" label="商品重量"> </el-table-column>
    <el-table-column prop="add_time" label="创建时间"> </el-table-column>
    <el-table-column label="操作">
      <template slot-scope="scope">
        <el-button
          size="mini"
          type="primary"
          icon="el-icon-edit"
          plain
        ></el-button>
        <el-button
          size="mini"
          type="danger"
          icon="el-icon-delete"
          plain
        ></el-button>
      </template>
    </el-table-column>
  </el-table>
  <!-- 分页组件 -->
  <el-pagination
    background
    layout="prev, pager, next"
    :total="total"
    :current-page="pagenum"
    :page-size="pagesize"
    @current-change="changePage"
  >
  </el-pagination>

  <!-- 添加商品对话框 -->

  </div>
</template>

<script>
export default {
  data(){
    return{
       goodsList: [],
       pagenum:1,
       pagesize:5,
       total:0
    }
  },
  created(){
    // console.log('当前页：', typeof this.$route.params.page)
    const pagenum = this.$route.params.page - 0
    this.getGoodsList(pagenum)
  },
  //监听路由的改变，只要路由发生改变，就执行这段代码逻辑  to获取到当前路由中的页码  然后，根据当前页来获取数据
  watch: {
    $route(to,from){
    const pagenum = to.params.page-0
    this.getGoodsList(pagenum)
   }
  },

  methods:{
    //分页获取商品列表信息
    async getGoodsList (pagenum = 1) {
      const res = await this.$http.get('/goods', {
        params: {
          query: '',
          pagenum,
          pagesize: this.pagesize
        }
      })

      const { goods, pagenum: curPage, total } = res.data.data

      this.goodsList = goods
      this.pagenum = curPage - 0
      this.total = total
    },

    //切换页面
   changePage(curPage){
     console.log(curPage)
    //  this.getGoodsList(curPage)
    this.$router.push(`/goods/${curPage}`)
    }
  }
}
</script>

<style>

</style>
