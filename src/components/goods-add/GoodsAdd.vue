<template>
  <div class="goods-add">
    <!-- 步骤条组件 -->
  <el-steps :active="active" finish-status="success">
    <el-step title="第一步" description="基本信息"></el-step>
    <el-step title="第二步" description="商品图片"></el-step>
    <el-step title="第三步" description="商品内容"></el-step>
  </el-steps>
  <!-- tabs标签页 -->
  <el-tabs v-model="tabActive" tab-position="left"  @tab-click="tabchange">
    <el-tab-pane label="基本信息" name="basic">
      <el-form :model="goodsAddForm" label-width="80px">
       <el-form-item label="商品名称">
         <el-input v-model="goodsAddForm.name"></el-input>
      </el-form-item>
      <el-form-item label="商品价格">
         <el-input v-model="goodsAddForm.name"></el-input>
      </el-form-item>
       <el-form-item label="商品重量">
         <el-input v-model="goodsAddForm.name"></el-input>
      </el-form-item>
       <el-form-item label="商品数量">
         <el-input v-model="goodsAddForm.name"></el-input>
      </el-form-item>
      <el-form-item label="商品分类">
       <el-cascader
            :options="cateList"
            v-model="goodsAddForm.goods_cat_add"
            :props="{
              label: 'cat_name',
              value: 'cat_id'
            }"
          >
          </el-cascader>
  </el-form-item>
      <el-form-item label="是否促销">
        <el-radio v-model="goodsAddForm.is_promote" :label="true">是</el-radio>
        <el-radio v-model="goodsAddForm.is_promote" :label="false">否</el-radio>
     </el-form-item>
</el-form>
      <el-button type="primary" @click="next(1,'pic')">下一步</el-button>
    </el-tab-pane>
    <el-tab-pane label="商品图片" name="pic">
      <!-- 上传图片组件 -->
      <el-upload
        action="http://localhost:8888/api/private/v1/upload"
        list-type="picture-card"
        :headers="headers"
        :on-success="onSuccess">
  <el-button size="small" type="primary">点击上传</el-button>
  <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
</el-upload>
      <el-button type="primary" @click="next(2,'content')">下一步</el-button>
    </el-tab-pane>
    <el-tab-pane label="商品内容" name="content">
      <!-- 富文本编辑器 -->
      <quill-editor v-model="goodsAddForm.goods_introduce"></quill-editor>
      <el-button type="primary">确定</el-button>
    </el-tab-pane>
  </el-tabs>
  </div>
</template>

<script>
import 'quill/dist/quill.core.css'
import 'quill/dist/quill.snow.css'
import 'quill/dist/quill.bubble.css'
import { quillEditor } from 'vue-quill-editor'
 export default {
   components:{
     quillEditor
   },
    data() {
      return {
        active: 0,
        tabActive:'',
        // tab 栏选中项的值
        tabActive: 'basic',
        //商品分类的数据
        cateList:[],
        //给商品添加数据对象
        goodsAddForm:{
        goods_name: '',
        goods_cat: '',
        goods_price: '',
        goods_number: '',
        goods_weight: '',
        goods_introduce: '',
        // 上传图片文件的临时路径数组
        pics: [],
        // 商品分类选中项数组
        goods_cat_add: [],
        // 是否促销：
        is_promote: false
        },
        //上传图片文件的请求头
        headers:{
        Authorization:localStorage.getItem('token')
        },
    }

    },
    created(){
      this.getCateList()
    },
  methods:{
    //tabs标签页切换事件
    tabchange(tab){
      console.log("tab-click")
      this.active = tab.index-0
    },
    //关联tabs标签页步骤条和下一步
    next(active,tabActive){
      this.active = active
      this.tabActive = tabActive
    },
    //获取分类列表数据
    async getCateList(){
      const res = await this.$http.get('/categories',{
        params:{
          type:3
        }
      })
      // console.log(res)
      this.cateList = res.data.data
    },
    //图片上传成功的回调函数
    onSuccess(response, file, fileList){
      console.log(response, file, fileList)
      this.goodsAddForm.pics.push({pic:response, file, fileList})
    }
  },

  }
</script>

<style>

</style>
