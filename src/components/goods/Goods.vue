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
          @click="showEditDialog(scope.row)"
        ></el-button>
        <el-button
          size="mini"
          type="danger"
          icon="el-icon-delete"
          plain
          @click="delGoods(scope.row.goods_id)"
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
  <!-- 编辑商品对话框 -->
    <el-dialog title="编辑商品" :visible.sync="isShowEditGoodsDialog">
  <el-form :model="goodsForm" label-width="100px">
    <el-form-item label="商品名称" >
      <el-input v-model="goodsForm.goods_name" autocomplete="off"></el-input>
    </el-form-item>
   <el-form-item label="商品价格" >
      <el-input v-model="goodsForm.goods_price" autocomplete="off"></el-input>
    </el-form-item>
    <el-form-item label="商品重量" >
      <el-input v-model="goodsForm.goods_weight" autocomplete="off"></el-input>
    </el-form-item>
    <el-form-item label="创建时间" >
      <el-input v-model="goodsForm.add_time" autocomplete="off"></el-input>
    </el-form-item>
  </el-form>
  <div slot="footer" class="dialog-footer">
    <el-button @click="showEditGoodsDialog = false">取 消</el-button>
    <el-button type="primary" @click="editGoods">确 定</el-button>
  </div>
</el-dialog>
  </div>
</template>

<script>
export default {
  data(){
    return{
       goodsList: [],
       pagenum:1,
       pagesize:5,
       total:0,
    isShowEditGoodsDialog:false,
       goodsForm:{
        goods_id:'',
        goods_name:'',
        goods_price:'',
        goods_weight:'',
        add_time:'',
        goods_number:'',
        cat_id:'',

       }
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

    //删除
    async delGoods(id){
      console.log(id)
     try{
      //显示弹出框
        await this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        })
        //发送请求，删除数据
          const res = await this.$http.delete(`/goods/${id}`)
          console.log(res)
         if(res.data.meta.status===200){
          //提示信息
          this.$message({
            type:'success',
            message:res.data.meta.msg
          })
          //刷新商品列表
          this.getGoodsList()
         }else{
          this.$message({
            type:"warning",
            message:res.data.meta.msg
          })
        }
    }catch(err){
        this.$message({
          type:"info",
          message:'取消删除'
        })
    }
  },
    //编辑对话框
    async showEditDialog(goods){
      // console.log('showEditDialog')
      this.isShowEditGoodsDialog = true
      //获取
      console.log(goods)
      for(let key in this.goodsForm){
        this.goodsForm[key] = goods[key]
      }
    },
    //编辑功能
    async editGoods (goods_id){
      console.log(goods_id)
      try{
        const {goods_id,goods_name,goods_price,goods_weight,add_time,goods_number, cat_id} = this.goodsForm
        console.log(goods_id);

        const res = await this.$http.put(`/goods/${goods_id}`,{
          goods_id,goods_name,goods_price,goods_weight,add_time,goods_number,cat_id
        })
        console.log(res)
      }catch(err){
      }
    },
    //切换页面
   changePage(curPage){
     console.log(curPage)
    //  this.getGoodsList(curPage)
      this.$router.push(`/goods/${curPage}`)
      }
    },
}
</script>

<style>

</style>
